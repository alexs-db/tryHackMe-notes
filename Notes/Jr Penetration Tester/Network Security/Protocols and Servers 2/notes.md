This room covered various protocols, their usage, and how they work under the hood. Three common attacks are:

- Sniffing Attack
- MITM Attack
- Password Attack

For each of the above, we focused both on the attack details and the mitigation steps.

Many other attacks can be conducted against specific servers and protocols. We will provide a list of some related modules:

- **Vulnerability Research**: This module provides more information about vulnerabilities and exploits.
- **Metasploit**: This module trains you on how to use Metasploit to exploit target systems.
- **Burp Suite**: This module teaches you how to use Burp Suite to intercept HTTP traffic and launch attacks related to the web.

It is good to remember the default port number for common protocols. For convenience, the services we covered are listed in the following table sorted by alphabetical order.

| Protocol | TCP Port | Application(s) | Data Security |
|----------|----------|----------------|---------------|
| FTP      | 21       | File Transfer  | Cleartext     |
| FTPS     | 990      | File Transfer  | Encrypted     |
| HTTP     | 80       | Worldwide Web  | Cleartext     |
| HTTPS    | 443      | Worldwide Web  | Encrypted     |
| IMAP     | 143      | Email (MDA)    | Cleartext     |
| IMAPS    | 993      | Email (MDA)    | Encrypted     |
| POP3     | 110      | Email (MDA)    | Cleartext     |
| POP3S    | 995      | Email (MDA)    | Encrypted     |
| SFTP     | 22       | File Transfer  | Encrypted     |
| SSH      | 22       | Remote Access and File Transfer | Encrypted |
| SMTP     | 25       | Email (MTA)    | Cleartext     |
| SMTPS    | 465      | Email (MTA)    | Encrypted     |
| Telnet   | 23       | Remote Access  | Cleartext     |

Hydra remains a very efficient tool that you can launch from the terminal to try different passwords. We summarize its main options in the following table.

| Option        | Explanation                                      |
|---------------|--------------------------------------------------|
| -l username   | Provide the login name                           |
| -P WordList.txt | Specify the password list to use               |
| server service | Set the server address and service to attack    |
| -s PORT       | Use in case of non-default service port number   |
| -V or -vV     | Show the username and password combinations being tried |
| -d            | Display debugging output if the verbose output is not helping |