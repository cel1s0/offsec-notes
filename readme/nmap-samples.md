---
description: There are some NMAP scan samples.
---

# Nmap samples

```
sudo nmap -sS -T4 -p- -sC -A -v -o nmap.txt 10.10.10.10

sudo nmap -sU -T4 -v -o nmapudp.txt 10.10.10.10

nmap --script smb-vuln-* -Pn -p 445 10.10.10.10

nmap --script smb-enum-shares -Pn -p 139,445 -o smb.txt 10.10.10.10

nmap --script "rdp-enum-encryption or rdp-vuln-ms12-020 or rdp-ntlm-info" -p 3389 -T4 -Pn 10.10.10.10

nmap --script=realvnc-auth-bypass --vv -p5900 10.10.10.10

nmap -p 27017 -script mongodb-* 10.10.10.10

nmap --script=nfs-* -p 2049 10.10.10.10

nmap --script ms-sql-info,ms-sql-empty-password,ms-sql-xp-cmdshell,ms-sql-config,ms-sql-ntlm-info,ms-sql-tables,ms-sql-hasdbaccess,ms-sql-dac,ms-sql-dump-hashes --script-args mssql.instance-port=1433,mssql.username=sa,mssql.password=,mssql.instance-name=MSSQLSERVER -sV -p 1433 10.10.10.10

nmap -p 1433 --script ms-sql-brute --script-args mssql.domain=DOMAIN,user=sa,passdb=~/Desktop/rockyou.txt,ms-sql-brute.brute-windows-accounts 10.10.10.10

nmap --script=nfs-showmount 10.10.10.0/24

nmap -n --script "(default and *dns*) or fcrdns or dns-srv-enum or dns-random-txid or dns-random-srcport" 10.10.10.10
```
