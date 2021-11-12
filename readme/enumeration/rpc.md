# RPC

[https://infinitelogins.com/2020/06/17/enumerating-smb-for-pentesting/](https://infinitelogins.com/2020/06/17/enumerating-smb-for-pentesting/)

Testing for Null or Authenticated Sessions:

To test for null sessions, you can use the following command. If it connects, then you'll be able to issue rpc client commands for further enumeration.

```
rpcclient -U "" -N [ip]
```

Have valid credentials? Use them to connect:

```
rpcclient -U <user> 10.10.10.193
```

Once connected, there are various queries you can run.

To enumerate printers:

```
enumprinters
```

To enumerate users and groups:

```
enumdomusers
enumdomgroups
```

The above command will output user/group RIDs. You can pass those into further queries like:

```
querygroup <RID>
querygroupmem <RID>
queryuser <RID>
```
