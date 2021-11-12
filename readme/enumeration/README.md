# Enumeration

```
sudo nmap -sS -T4 -p- -sC -A -v -o nmap.txt 10.10.10.10
sudo nmap -sU -T4 -v -o nmapudp.txt 10.10.10.10
```

### 139, 445 -> SMB

```
nmap --script smb-vuln-* -Pn -p 445 10.10.10.10
nmap --script smb-enum-shares -Pn -p 139,445 -o smb.txt 10.10.10.10

enum4linux 10.10.10.10
enum4linux -u 'Guest' -p '' -a 10.10.10.10

smbclient \\\\10.10.10.10\\somedir -U Guest
smbclient -L 10.10.10.10 -U "" -N

smbmap -H 10.10.10.10
smbmap -u Guest -H 10.10.10.10

smbtree -S 10.10.10.10 -U Guest
```

### HTTP

```
nikto -host http://10.10.10.10/ -output nikto.txt

gobuster dir -u http://10.10.10.10/ -w /usr/share/wordlists/dirb/big.txt -o gobuster.txt
-k ignore ssl (for https)
-w ~/Desktop/directory-list-2.3-medium.txt
-w ~/Desktop/directory-list-1.0.txt
-x asp,aspx (extension)

dirb http://10.10.10.10/ -X .asp -o dirb.txt

dirbuster -u http://10.10.10.10/ -e asp -v -t 50
dirbuster
```

