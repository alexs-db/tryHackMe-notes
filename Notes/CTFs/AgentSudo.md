# TryHackMe CTF: Agent Sudo — Walkthrough

Hi! It is time to look at the Agent Sudo CTF on TryHackMe. I am making these walkthroughs to keep myself motivated to learn cyber security, and ensure that I remember the knowledge gained by THMs rooms. Join me on learning cyber security. I will try and explain concepts as I go, to differentiate myself from other walkthroughs.

Agent sudo! He’s a cute one.  
Room URL: [https://tryhackme.com/room/agentsudoctf](https://tryhackme.com/room/agentsudoctf)

## Task 1 (Author note)
Not much more to do here than so say hi, and let’s have some fun!

### Questions
**Deploy the machine**  
Answer: No answer needed

## Task 2 (Enumerate)
Enumerate the machine and get all the important information

### Questions
**How many open ports?**

I first tried running nmap with the `-sn` flag. This did not return any results, as this is likely due to the machine not responding to pings. Therefore I switched to using the `-Pn` flag which treats all hosts as online.

**Scanning for services**  
Let’s get more info on them by using the `-A` flag.

**Scanning for more info on the 3 services**  
Three ports are open.  
Answer: 3

**How you redirect yourself to a secret page?**

We can see a service running on http, so this is a website we can visit in our browser.

**Visiting the webpage**  
Well, we get a hint here. User-agent is a header we can set.  
Answer: user-agent

**What is the agent name?**

We can use Burp Suite to intercept the request and edit the User-Agent header before sending it forward.

I thought it would be smart to use R as User-Agent as that is the name written on the main page. This gives us the following:

This is actually a hint. Since the head agent is called R, and he mentions 25 other employees, my assumption was that all agents are called by a letter.

I first tried adding A as user-agent, followed by B. This did nothing. But adding C redirects us to the following page:

**Finding the secret page**  
Answer: Chris

## Task 3 (Hash cracking and brute-force)
Done enumerating the machine? Time to brute your way out.

### Questions
**FTP password**

We know that the username of the agent is either C or chris. Let’s try chris first as C is probably too short of a username. We will use hydra to crack the password, although we could probably also use a Metasploit module (ftp_login) or other tools.

```sh
hydra -l chris -P /usr/share/wordlists/rockyou.txt ftp://<target ip>
```

This quickly gave us the password:

**Cracking the ftp password with hydra**  
Answer: crystal

**Zip file password**

Let’s enter the FTP with our newly acquired credentials:

**Logging into the FTP service**  
You can use `mget *` to download all files.

Now we can read the txt file:

**Reading the To_agentJ.txt file**  
It points us to a fake and a real picture. The fake picture hides the login password for Agent J.

There are different terminal commands to investigate the images. We can use `file`, but nothing seems strange in its output. Another possibility is using `exiftool`, which helps us to read meta information:

```sh
exiftool cutie.png
```

**Running exiftool**  
It mentions a warning: Trailer data after PNG IEND chunk.

Let’s dump the file to hex format by using `xxd`.

```sh
xxd cutie.png
```

**Running xxd**  
Hmm..some interesting text. This can also be seen when running `strings cutie.png`.

The last tool we can use is `binwalk`. Binwalk is a tool that allows you to search binary images for embedded files and executable code. Let’s try this out:

```sh
binwalk cutie.png
```

**Running binwalk**  
There we have it! We have a hidden zip file hidden in the image, which hides a txt file called To_agentR.txt.

We can extract the file by running the same command, together with the `-e` flag.

```sh
binwalk -e cutie.png
```

We can find the files in the `_cutie.png.extracted` folder.

**Looking at the extracted zip file contents**  
We can use the `zip2john` tool to convert the zip to a format suitable for john.

```sh
zip2john 8702.zip > hash.txt
```

Now we can crack it with john:

```sh
john hash.txt
```

**Cracking the zip file hash with john**  
We got the password. It took quite a few steps to get here. Good job :)  
Answer: alien

**steg password**

Now we can open the zip file and read the txt file. We can do this with the following command:

```sh
7z e 8702.zip
```

This gives us the file, which we can read with `cat`.

**Reading the To_agentR.txt file**  
More clues. `QXJlYTUx` looks out of the ordinary. It looks encoded somehow.

We can try decoding it here:

[Base64 Decode and Encode - Online](https://www.base64decode.org/)

This gives us `area51`.  
Answer: area51

**Who is the other agent (in full name)?**

Now, this one was a bit trickier to be honest. We need to use `steghide` together with a passphrase to find hidden files in image/audio files.

Run:

```sh
steghide extract -sf cute-alien.jpg
```

**Finding the secret message with steghide**  
We find a message, together with a username and password!  
Answer: james

**SSH password**  
Answer: hackerrules!

## Task 4 (Capture the user flag)
You know the drill.

### Questions
**What is the user flag?**

This one is easy. Simply login to the SSH service with the username and password discovered in the previous step:

**Logging into the SSH service**  
Answer: b03d975e8c92a7c04146cfa7a5a313c7

**What is the incident of the photo called?**

Exit the SSH service, and copy the jpg file from the SSH service to your system with the following command:

```sh
sudo scp james@<target ip>:Alien_autospy.jpg ~/
```

**Copying the image to our system**  
Now it is time to do a reverse image search at [https://images.google.com/](https://images.google.com/).

**Reverse image searching**

**Article using the image**  
Answer: Roswell alien autopsy

## Task 5 (Privilege escalation)
Enough with the extraordinary stuff? Time to get real.

### Questions
**CVE number for the escalation (Format: CVE-xxxx-xxxx)**

Log back in on the SSH service with james. Try and see what privileges james has by running `sudo -l`.

**Checking out james’ privileges**  
`(ALL, !root) /bin/bash` sounds interesting!

Let’s see if we can find out more by googling. I came across the following page on exploitdb:

[Offensive Security's Exploit Database Archive](https://www.exploit-db.com/exploits/47502)

This has the following CVE number: `CVE-2019–14287`. It allows us to get root access by running:

```sh
sudo -u#-1 /bin/bash
```

Answer: CVE-2019–14287

**What is the root flag?**

Gain root access by entering the above command. Then change directory to the root and find the root.txt file.

**Reading the root flag**  
Answer: b53a02f55b57d4439e3341834d70c062

**(Bonus) Who is Agent R?**  
Answer: DesKel