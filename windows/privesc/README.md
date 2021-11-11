---
description: Checklist for privilege escalation in Windows
---

# Checklist - PrivEsc

### Checklist

* Uncommon directories under C directory
* Installed vulnerable programs
* Unquoted service paths
* Exploitable build version
* SeImpersonateToken or SeAssignPrimaryToken - Enabled
* Jobs with editable files
* Weak passwords at Filezilla FTP
* MSSQL is running with sa user
* Misconfigured LDAP

### System Version Number

```
winver

REG QUERY "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion" /v ReleaseId
```

### Windows Exploit Suggester

[windows-exploit-suggester.py](https://github.com/AonCyberLabs/Windows-Exploit-Suggester)

```
systeminfo >> output to text file in local machine
./windows-exploit-suggester.py output.txt
```

### Finding Out Process Name Which Is Listening On TCP Or UDP Port

[https://stackoverflow.com/questions/48198/how-can-you-find-out-which-process-is-listening-on-a-tcp-or-udp-port-on-windows](https://stackoverflow.com/questions/48198/how-can-you-find-out-which-process-is-listening-on-a-tcp-or-udp-port-on-windows)

```
Get-Process -Id (Get-NetTCPConnection -LocalPort 65535).OwningProcess
```

### Disabling Firewall (Need High Priv.)

```
netsh advfirewall set allprofiles state off
```

### Excluding the folder from Defender (Need High Priv.)

```
powershell -inputformat none -outputformat none -NonInteractive -Command Add-MpPreference -ExclusionPath "C:\Users\Administrator\Desktop"
```

