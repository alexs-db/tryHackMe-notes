# Port Scan

I did a basic NMap service version detection scan and it gave me enough to start on.

```bash
nmap -sV IP
```

It gave the following results:

```
PORT      STATE SERVICE  VERSION
22/tcp    open  ssh      OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
25/tcp    open  smtp     Postfix smtpd
110/tcp   open  pop3     Dovecot pop3d
143/tcp   open  imap     Dovecot imapd (Ubuntu)
993/tcp   open  ssl/imap Dovecot imapd (Ubuntu)
995/tcp   open  ssl/pop3 Dovecot pop3d
4000/tcp  open  http     Node.js (Express middleware)
50000/tcp open  http     Apache httpd 2.4.41 ((Ubuntu))
MAC Address: 02:4A:C1:49:20:F3 (Unknown)
Service Info: Host:  mail.filepath.lab; OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Quite some services exposed there, huh!

According to my experience, in the easy and medium CTFs, generally we find something from the webapp which then helps us to get some usernames and passwords which can then be used to SSH or something.

# Website FAFO

## Port 50000

On visiting port 50000 we find a SysMon [System Monitoring] portal. It was a restricted portal, which meant it required some authentication. On visiting the login section, it was a basic login page.

Directory brute forcing with Gobuster revealed the following results:

```bash
gobuster dir -w /usr/share/wordlists/SecLists/Discovery/Web-Content/big.txt -u http://10.10.150.93:50000
```

The only sensible step next was to visit the uploads directory which only had the profile picture image.

## Port 4000

So, I decided to visit the website running at port 4000 to see if it was any good….it was. It opened up a login page. Strangely the creds for a guest login was provided: `guest/guest`.

After logging in, it opened up a “Review App”. Here we can see My profile, which is a guest (obviously). Under my profile there were 2 available friends and an option to add them as friends and at the bottom there was a My Friends section which was empty.

Being the kind guy I am, I quickly added them as friends and they appeared under the My Friends section. Now, we could also access their profiles. On checking their profiles I found out none of them were admins.

Now let's check out the recommend activity section. It has 2 fields for an activity type and activity name respectively. I entered `test` and `test2` respectively and it appeared under the Friend Details.

After this, I thought exactly what you’re thinking rn, screwed with the `isAdmin` field. The `isAdmin` changed to true but nothing special happened. I tried the same thing with Marry and my own guest profile. No luck with Marry but in the guest profile, changing the `isAdmin` to true, opened two more sections — Settings and API.

The API section revealed something very interesting. Here we get, what is likely an internal API. The first one is an example and the second one is something juicy.

> **QuickInfo:** An internal API (Application Programming Interface) is a set of protocols, tools, and definitions that allows different parts of a software application to communicate with each other within the confines of the organization or application itself. So, Internal APIs are accessible only within the organization’s network or by authenticated users with specific permissions.

Now all we need is a place to make these internal requests. Visiting the Settings sections, we find just that. Basically, what this does is accept an image URL, downloads the image and sets it as the banner image. If there are no filters in place, maybe we can post the API link in there and get the response as it is part of the internal infrastructure.

```
http://127.0.0.1:5000/getAllAdmins101099991
```

Upon clicking on Update Banner Image, we receive the following response:

```
data:application/json; charset=utf-8;base64,eyJSZXZpZXdBcHBVc2VybmFtZSI6ImFkbWluIiwiUmV2aWV3QXBwUGFzc3dvcmQiOiJhZG1pbkAhISEiLCJTeXNNb25BcHBVc2VybmFtZSI6ImFkbWluaXN0cmF0b3IiLCJTeXNNb25BcHBQYXNzd29yZCI6IlMkOSRxazZkIyoqTFFVIn0=
```

Decoding the base64 encoded string we get 2 passwords as expected:

```bash
echo "eyJSZXZpZXdBcHBVc2VybmFtZSI6ImFkbWluIiwiUmV2aWV3QXBwUGFzc3dvcmQiOiJhZG1pbkAhISEiLCJTeXNNb25BcHBVc2VybmFtZSI6ImFkbWluaXN0cmF0b3IiLCJTeXNNb25BcHBQYXNzd29yZCI6IlMkOSRxazZkIyoqTFFVIn0=" | base64 --decode
```

## Going back to the SysMon portal @ port 50000

With the above creds I was able to login as administrator in the SysMon portal where I got a flag. It opens up a dashboard with CPU, RAM and disk space monitoring. After spending some time figuring out what to do with this dashboard, I noticed something interesting in the source code (see white arrow). It is the source of the profile image. I opened the image in a new tab. Playing around with the ‘img’ query parameter may expose sensitive files.

So after spending a LOT of time manipulating the file path (LFI poisoning), I came up with a payload which exposed `/etc/passwd`. [Thanks to the list by Jhaddix, which contains LFI payloads]

> [SecLists/Fuzzing/LFI/LFI-Jhaddix.txt at master · danielmiessler/SecLists](https://github.com/danielmiessler/SecLists/blob/master/Fuzzing/LFI/LFI-Jhaddix.txt)

You can automate LFI poisoning using this list with BurpSuite since that would take a lot of time and there is rate limiting in the community edition, I copied and pasted some payloads (which looked good to me) exposing the `/etc/passwd` file.

This one worked:

```
....%2F%2F....%2F%2F....%2F%2F....%2F%2F....%2F%2F....%2F%2F....%2F%2F....%2F%2F....%2F%2Fetc%2Fpasswd
```

I found out 2 users `joshua` and `charles`.

After finding the usernames the next best thing I thought would be to brute force SSH passwords.

# SSH

For brute forcing, my go-to tool is hydra. I used the `fasttrack.txt` wordlist.

```bash
hydra -l joshua -P /usr/share/wordlists/fasttrack.txt <IP> ssh
```

And boom we got a matching password for Joshua.

> [You will see different IPs as I had to restart the machines]

Log in to ssh with the password:

```bash
ssh joshua@<IP>
```

We can find the hidden text file (flag) in the `/var/www/html` as mentioned in the second question.

# Summary

To summarize this box, an Nmap scan revealed an ssh service and two webservers running at port 4000 and 50000. In port 4000, changing the `isAdmin` field to ‘true’ in the guest account led to administrator privileges subsequently opening an API and settings section. Fetching the API from the settings section gave us base64 encoded admin password for the SysMon login portal at port 50000. Logging in to SysMon portal revealed a dashboard containing the first flag. Checking the source code we found that the profile picture of the admin was a png file that was requested by a query parameter ‘img’. LFI injection revealed the `/etc/passwd` file which in turn revealed 2 usernames Joshua and Charles. A valid password for joshua was found for SSH login. The second flag was found in the `/var/www/html` directory.
