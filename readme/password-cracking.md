# Password Cracking

[https://crackstation.net\
https://www.tunnelsup.com/hash-analyzer/\
https://github.com/d0n601/Pentest-Cheat-Sheet](https://crackstation.nethttps/www.tunnelsup.com/hash-analyzer/https://github.com/d0n601/Pentest-Cheat-Sheet)

Password Cracking /etc/shadow

Hash algorithm cheat sheet test:$1$......

```
$1$ is MD5 (md5crypt)
$2a$ is Blowfish
$2y$ is Blowfish
$5$ is SHA-256
$6$ is SHA-512
```

### JOHNTHERIPPER

```
unshadow passwd shadow > hashes
john --wordlist=~/Desktop/rockyou.txt hashes

john --list=formats | grep SHA
john --format=Raw-SHA256 hash.txt --wordlist=~/Desktop/rockyou.txt 
```

### HASHCAT

{% embed url="https://hashcat.net/wiki/doku.php?id=example_hashes" %}

**400 phpass, WordPress (MD5), Joomla (MD5) $P$984478476IagS59wHZvyQMArzfx58u.**

hashcat -m 400 -w 4 -a 0 hash /usr/share/wordlists/rockyou.txt --force

**500 md5crypt, MD5 (Unix), Cisco-IOS $1$ (MD5) 2 $1$28772684$iEwNOgGugqO9.bIz5sk8k/**

hashcat -m 500 -a 0 hash /usr/share/wordlists/rockyou.txt --force

**1000 NTLM b4b9b02e6f09a9bd760f388b67351e2b**

hashcat -m 1000 -w 4 -a 0 hash /usr/share/wordlists/rockyou.txt --force

**1600 Apache $apr1$ MD5, md5apr1, MD5 (APR) 2 $apr1$71850310$gh9m4xcAn3MGxogw**

hashcat -m 1600 -w 4 -a 0 hash /usr/share/wordlists/rockyou.txt --force

**5600 NetNTLMv2 admin::N46iSNekpT:08ca45b7d7ea58ee:88dc...**

hashcat -m **5**600 -w 4 -a 0 hash /usr/share/wordlists/rockyou.txt --force

**13100 Kerberos 5, etype 23, TGS-REP $krb5tgs$23$**_**user$realm$test/spn**_**$63386d22d359fe422...**

hashcat -m 13100 -a 0 hash.txt /usr/share/wordlists/rockyou.txt --force
