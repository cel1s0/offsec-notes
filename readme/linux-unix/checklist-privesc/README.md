---
description: Checklist for privilege escalation in Linux
---

# Checklist - PrivEsc

### Priv Esc Scripts

[linenum.sh](https://github.com/rebootuser/LinEnum)

[linpeas.sh](https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS)

[linux-exploit-suggester.sh](https://github.com/mzet-/linux-exploit-suggester)

### Exploitable Kernel Detection

```
uname -a
cat /proc/version
cat /etc/*release
```

### Important Points

```
sudo --version

sudo -l (if you have user's password)

ls -lha /etc/passwd
ls -lha /etc/shadow

cat /etc/crontab

netstat -antup
netstat -tulpn

ls -lahR /home/
ls -lahR /var/

find / -iname secret 
find / -iname password
```

### Look into these directories to find valuable information.

```
/home
/srv
/opt
/media
/mnt
/var
/var/www
/etc/apache2
```

### Look processes with root privileges.

```
ps aux | grep root (or another user)
```

### Look files with SUID bit

```
# https://gtfobins.github.io/
# https://www.hackingarticles.in/linux-privilege-escalation-using-suid-binaries/

find / -perm -u=s -type f 2>/dev/null
```
