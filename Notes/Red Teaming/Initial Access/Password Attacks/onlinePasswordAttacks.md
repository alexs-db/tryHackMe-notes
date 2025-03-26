# Online Password Attacks

Online password attacks involve guessing passwords for networked services that use a username and password authentication scheme, including services such as HTTP, SSH, VNC, FTP, SNMP, POP3, etc. This section showcases using Hydra, a common tool used in attacking logins for various network services.

## Hydra

Hydra supports an extensive list of network services to attack. Using Hydra, we'll brute-force network services such as web login pages, FTP, SMTP, and SSH in this section. Often, within Hydra, each service has its own options, and the syntax Hydra expects takes getting used to. It's important to check the help options for more information and features.

---

### FTP

In the following scenario, we will perform a brute-force attack against an FTP server. By checking the Hydra help options, we know the syntax of attacking the FTP server is as follows:

```bash
hydra -l ftp -P passlist.txt ftp://10.10.x.x
```

- `-l ftp`: Specifies a single username. Use `-L` for a username wordlist.
- `-P`: Specifies the full path of the wordlist. You can specify a single password by using `-p`.
- `ftp://10.10.x.x`: The protocol and the IP address or the fully qualified domain name (FQDN) of the target.

**Tip:** Sometimes you don't need to brute-force and could first try default credentials. Try to attack the FTP server on the attached VM and answer the question below.

---

### SMTP

Similar to FTP servers, we can also brute-force SMTP servers using Hydra. The syntax is similar to the previous example, with the only difference being the targeted protocol. If you want to try other online password attack tools, you may need to specify the port number, which is 25. Make sure to read the help options of the tool.

```bash
hydra -l email@company.xyz -P /path/to/wordlist.txt smtp://10.10.x.x -v
```

**Example Output:**
```
[25][smtp] host: 10.10.x.x   login: email@company.xyz password: xxxxxxxx
```

---

### SSH

SSH brute-forcing can be common if your server is accessible to the Internet. Hydra supports many protocols, including SSH. We can use the previous syntax to perform our attack. It's important to notice that password attacks rely on having an excellent wordlist to increase your chances of finding a valid username and password.

```bash
hydra -L users.lst -P /path/to/wordlist.txt ssh://10.10.x.x -v
```

**Example Output:**
```
[22][ssh] host: 10.10.x.x   login: victim   password: xxxxxxxx
```

---

### HTTP Login Pages

In this scenario, we will brute-force HTTP login pages. To do that, first, you need to understand what you are brute-forcing. Using Hydra, it is important to specify the type of HTTP request, whether GET or POST. Checking Hydra options (`hydra http-get-form -U`), we can see that Hydra has the following syntax for the `http-get-form` option:

```
<url>:<form parameters>:<condition string>[:<optional>[:<optional>]
```

**Example Command:**
```bash
hydra -l admin -P 500-worst-passwords.txt 10.10.x.x http-get-form "/login-get/index.php:username=^USER^&password=^PASS^:S=logout.php" -f
```

- `-l admin`: Specifies a single username. Use `-L` for a username wordlist.
- `-P`: Specifies the full path of the wordlist. You can specify a single password by using `-p`.
- `10.10.x.x`: The IP address or the fully qualified domain name (FQDN) of the target.
- `http-get-form`: The type of HTTP request, which can be either `http-get-form` or `http-post-form`.
- `/login-get/index.php`: The path of the login page on the target webserver.
- `username=^USER^&password=^PASS^`: The parameters to brute-force. Use `^USER^` to brute-force usernames and `^PASS^` for passwords from the specified dictionary.
- `S=logout.php`: The success condition to identify the valid credentials.
- `-f`: Stops the brute-forcing attacks after finding a valid username and password.

**Tip:** Analyze the HTTP request using browser dev tools or a web proxy such as Burp Suite to determine the success and failure conditions.

---

### Other Tools

It is worth checking other online password attack tools to expand your knowledge, such as:

- **Medusa**
- **Ncrack**
- **Others**