# Local File Inclusion (LFI)

LFI attacks often occur due to a lack of security awareness among developers. In PHP, functions like `include`, `require`, `include_once`, and `require_once` can lead to vulnerabilities. While this example focuses on PHP, LFI vulnerabilities can also occur in other languages such as ASP, JSP, or Node.js. LFI exploits follow similar concepts to path traversal.

## Exploiting LFI

### Scenario 1: Basic LFI

Consider a web application that allows users to select between two languages, EN and AR:

```php
<?php 
    include($_GET["lang"]);
?>
```

The above PHP code uses a GET request via the URL parameter `lang` to include a file. For example:
- `http://webapp.thm/index.php?lang=EN.php` loads the English page.
- `http://webapp.thm/index.php?lang=AR.php` loads the Arabic page.

If there is no input validation, an attacker can access any readable file on the server. For instance, to read the `/etc/passwd` file, the attacker can use:
- `http://webapp.thm/get.php?file=/etc/passwd`

### Scenario 2: Directory-Specified LFI

In another example, the developer specifies the directory inside the function:

```php
<?php 
    include("languages/". $_GET['lang']); 
?>
```

Here, the `include` function calls PHP pages only from the `languages` directory via the `lang` parameter. Without input validation, an attacker can manipulate the URL to include sensitive files. For example:
- `http://webapp.thm/index.php?lang=../../../../etc/passwd`

This payload exploits the lack of input validation to include files from outside the intended directory.

### Scenario 3: Error-Based LFI

In this scenario, we perform black box testing without access to the source code. Errors are significant in understanding how data is processed. For example, an invalid input like `THM` might produce an error disclosing the include function's behavior and the web application's directory path.

To exploit this, we use the `../` trick to traverse directories and the Null Byte (`%00`) to bypass file type restrictions. For example:
- `http://webapp.thm/index.php?lang=../../../../etc/passwd%00`

### Scenario 4: Filtered LFI

If the `/etc/passwd` file is filtered, we can bypass this using the Null Byte or directory traversal tricks. For example:
- `http://webapp.thm/index.php?lang=/etc/passwd/.`
- `http://webapp.thm/index.php?lang=/etc/passwd%00`

### Scenario 5: Input Validation

If the developer filters keywords or replaces `../` with an empty string, we can use multiple traversal sequences to bypass it. For example:
- `http://webapp.thm/index.php?lang=....//....//....//....//etc/passwd`

### Scenario 6: Defined Directory Inclusion

If the web application forces the include to read from a defined directory, we need to include the directory in the payload. For example:
- `http://webapp.thm/index.php?lang=languages/../../../../../etc/passwd`

