# Port Forwarding

### From target machine

Secure way.

8080 port is only accessible from localhost on target machine. We can access it from our 8080 port via using this command. We can use this command at target machine.

We can add another ports with -R parameter. -R 192.168.1.1:10000:127.0.0.1:10000

```
ssh -N -R 192.168.1.1:8080:127.0.0.1:8080 cel1s0@192.168.1.1
```

### To target machine

If we know target machine's ssh credential, we can use this command.

We can add another ports with -L parameter. -L 8080:localhost:8080

```
ssh -L 5901:localhost:5901 test@192.168.1.2
```
