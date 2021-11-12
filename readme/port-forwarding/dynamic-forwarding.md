---
description: >-
  Port Forwarding From Target Machine With SSH - Secure Way - Specify Ports with
  -R Option
---

# Dynamic Forwarding

### ON TARGET MACHINE

```
cd /tmp
mkdir keys
ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/var/www/.ssh/id_rsa): /tmp/keys/id_rsa
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /tmp/keys/id_rsa.
Your public key has been saved in /tmp/keys/id_rsa.pub.
The key fingerprint is:
SHA256:p8BVmFym2cu0ORHMAZ4xxXDqHMbow9lkc6BXGfPbITI www-data@test
The key's randomart image is:
+---[RSA 2048]----+
|       .*@%+     |
|       =+#=+     |
|      o / E o .  |
|     + @ * B + . |
|      B S B . .  |
|       o o .     |
|        .        |
|                 |
|                 |
+----[SHA256]-----+
cd keys
ls
id_rsa
id_rsa.pub
cat id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDW6xqIeLknr+RcRMTYkkmMJZcHiZXVT7AxIKu8KMPeieZ+g8yMsBGeeeF4igNQOcOlPuYyY6HoMPGIThVxQBT5uPNQANAUGQE5MmZXTytkOzztYMxtPq3TjxhBxbd6rmEK/ApqREVo9NoGCi4TibfOuCpy3iY18w46F/bUzj2r2STytHvaUBhkS6pMh11WE18XC+ZQea0zOrRm6Nu+eDn3mwOYIaL4RHPKbnT6g55UzV8qNjtZRDRpkUww5SQwQIjoibgbZ0Qr/wT864kVUgOuthOKqJanwkKIo+URRp7c6E8i5d6y1CZIHU7touPEucJXFgfCdeda9PCDUpedlI39 www-data@test
```

### ON LOCAL MACHINE

```
$ cat ~/.ssh/authorized_keys
from="192.168.1.2",command="echo 'This account can only be used for port forwarding'",no-agent-forwarding,no-X11-forwarding,no-pty ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDW6xqIeLknr+RcRMTYkkmMJZcHiZXVT7AxIKu8KMPeieZ+g8yMsBGeeeF4igNQOcOlPuYyY6HoMPGIThVxQBT5uPNQANAUGQE5MmZXTytkOzztYMxtPq3TjxhBxbd6rmEK/ApqREVo9NoGCi4TibfOuCpy3iY18w46F/bUzj2r2STytHvaUBhkS6pMh11WE18XC+ZQea0zOrRm6Nu+eDn3mwOYIaL4RHPKbnT6g55UzV8qNjtZRDRpkUww5SQwQIjoibgbZ0Qr/wT864kVUgOuthOKqJanwkKIo+URRp7c6E8i5d6y1CZIHU7touPEucJXFgfCdeda9PCDUpedlI39 www-data@test

$ sudo systemctl start ssh.service
```

### ON TARGET MACHINE

```
ssh -f -N -R 1122:10.10.10.10:22 -R 13306:10.10.10.10:3306 -o "UserKnownHostsFile=/dev/null" -o "StrictHostKeyChecking=no" -i /tmp/keys/id_rsa cel1s0@192.168.1.1
Warning: Permanently added '192.168.1.1' (ECDSA) to the list of known hosts.
cel1s0@192.168.1.1's password: 
```

### ON LOCAL MACHINE

```
$ netstat -tulpn        
(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
LISTEN      -                   
tcp        0      0 127.0.0.1:13306         0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:1122          0.0.0.0:*   
```
