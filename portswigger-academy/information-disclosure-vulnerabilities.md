---
description: https://portswigger.net/web-security/information-disclosure
---

# Information disclosure vulnerabilities

Information disclosure, also known as information leakage, is when a website unintentionally reveals sensitive information to its users. Depending on the context, websites may leak all kinds of information to a potential attacker, including:

* Data about other users, such as usernames or financial information
* Sensitive commercial or business data
* Technical details about the website and its infrastructure

### Lab: Information disclosure in error messages

This lab's verbose error messages reveal that it is using a vulnerable version of a third-party framework.

`https://acfb1f791e56cd67c1a0ce5d00540000.web-security-academy.net/product?productId=test`

We sent string with productId parameter, then we got an error page. The id has to be a decimal value.

### Lab: Information disclosure on debug page

This lab contains a debug page that discloses sensitive information about the application.

In pages, there is a commented line which is following.

```
<!-- <a href=/cgi-bin/phpinfo.php>Debug</a> -->
```

### Lab: Source code disclosure via backup files

This lab leaks its source code via backup files in a hidden directory.

In robots.txt, there is a hidden directory.

```
User-agent: *
Disallow: /backup
```

`https://ac011f9f1f8cc651c02928c200100093.web-security-academy.net/backup/ProductTemplate.java.bak`

### Lab: Authentication bypass via information disclosure

This lab's administration interface has an authentication bypass vulnerability, but it is impractical to exploit without knowledge of a custom HTTP header used by the front-end.

`GET /admin` - Admin interface only available to local users

`TRACE /admin`

`X-Custom-IP-Authorization: 199.199.199.199`

→ Add this line to requests → Proxy → Options → Match and Replace → Add to replace option

`X-Custom-IP-Authorization: 127.0.0.1`

### Lab: Information disclosure in version control history

This lab discloses sensitive information via its version control history.

There is .git directory.

`wget -r https://ac011fd31e74cd34c168d40200460027.web-security-academy.net/.git`

opened with qgit

we can see these:

```
-ADMIN_PASSWORD=l6uzbyjcfqpwowi43ygv
+ADMIN_PASSWORD=env('ADMIN_PASSWORD')
```
