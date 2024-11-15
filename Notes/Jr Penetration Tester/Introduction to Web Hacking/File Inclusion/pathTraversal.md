# Path Traversal

Path Traversal, also known as Directory Traversal, is a web security vulnerability that allows attackers to read files on the server running an application. This is done by manipulating the web application's URL to access files outside the application's root directory.

## How It Works

Path traversal vulnerabilities occur when user input is passed to functions like `file_get_contents` in PHP without proper validation. Attackers exploit this by adding payloads to the URL, such as `../`, to move up directories and access restricted files.

### Example

For a web application storing files in `/var/www/app`, an attacker might use:
```
http://webapp.thm/get.php?file=../../../../etc/passwd
```
This would retrieve the `/etc/passwd` file if input validation is not properly implemented.

### Windows Example

On a Windows server, an attacker might use:
```
http://webapp.thm/get.php?file=../../../../boot.ini
```
to access the `c:\boot.ini` file.

## Common Files to Test

- `/etc/issue`: System identification message
- `/etc/profile`: System-wide default variables
- `/proc/version`: Linux kernel version
- `/etc/passwd`: Registered users
- `/etc/shadow`: User passwords
- `/root/.bash_history`: Root user command history
- `/var/log/dmessage`: System messages
- `/var/mail/root`: Root user emails
- `/root/.ssh/id_rsa`: Private SSH keys
- `/var/log/apache2/access.log`: Apache access logs
- `C:\boot.ini`: Boot options for BIOS firmware

Proper input validation and filtering are crucial to prevent path traversal vulnerabilities.
