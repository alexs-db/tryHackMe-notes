Once we start the machine, as mentioned, we need to add the IP in our `/etc/hosts` file of our attacker machine.

Honestly, this indicates that we should try for subdomain enumeration. But, let's start with visiting the site first. There was nothing except an image of a brick. Let's look into the source page:

### Page Source

The site shows `wp-content` which indicates the presence of WordPress. The first thing we should do now is to scan this website using `wpscan`:

```bash
wpscan --url http://example.com --disable-tls-checks
```

Now, it will scan:

Here, it finds a theme: `bricks` with version: `1.9.5`. Simply, google it for any vulnerability present:

### Google Search for Exploit

The first link shows the POC for the vulnerability. We will move ahead with the second one, as it has the exploit available. Copy the raw code and paste it in a file in our attacker machine.

Make sure to give executable permissions to the file.

Now, run the exploit:

```bash
chmod +x exploit.sh
./exploit.sh
```

Ok, forgot to mention that we need to install some requirements in order for this exploit to work:

```bash
pip install -r requirements.txt
```

Now, it should work perfectly:

### Initial Access

Here, we can see that we have initial access on the system as the `apache` user. But this shell is not a stable shell, as we can’t use `cd`.

So, what we can do is to take a simple reverse shell from this initial access:

Make sure to run a listener in another terminal to receive the connection.

```bash
nc -lvnp 4444
```

### Reverse Shell

And now, we will see that we have stable shell access:

```bash
ls -la
```

On doing `ls`, we can see our hidden `.txt` file. This will give the answer to our first question.

Now, one can also notice some more attractive files present there. I opened the `wp-config.php` file, and guess what, it has the credentials hardcoded:

```php
define('DB_USER', 'username');
define('DB_PASSWORD', 'password');
```

To use this, one can head on to `https://bricks.thm/phpmyadmin` and login with these credentials:

It will give access to the admin portal from where one can alter the settings, login to the wp-admin panel, and a lot more.

But these won’t lead to the answer for our next question. For that, I simply searched on Google:

### Google Search for Service List

So, let's try this command on the terminal:

```bash
ps aux
```

It will list all the processes which are currently running. On scrolling down one can easily find the odd one out:

### Running Services

This gives the service name affiliated with the suspicious process. Let's `cat` out the content of this service:

```bash
cat /lib/NetworkManager/suspicious_service
```

This gives the answer to our second question.

One thing to note here is, this service is executing from `/lib/NetworkManager` directory. Let's head on to that, we may get the answers for the next questions:

It has a lot of files. But the point to note here is, that only `inet.conf` file has read-only permissions. Let's `cat` it out:

```bash
cat /lib/NetworkManager/inet.conf
```

It was showing all the logs stored which confirms that the log file which we are searching for the miner instance is `inet.conf`.

It is better to use `head`, instead of `cat` to see the contents.

```bash
head /lib/NetworkManager/inet.conf
```

We can easily see the ID present over there which seems to be encoded.

Let's copy it and try to decode it in CyberChef:

### CyberChef

I tried the automatic analyzer of CyberChef to decode it and guess what, it decoded it successfully:

### Decoding the ID

Here, something was unusual. We can see the terms repeating them after a certain point. Since we are in search of a wallet address. I googled for the size of the address:

### Wallet Address Size Google

But the address we have is more than 62. So, I tried splitting them into two parts:

### Wallet Address

Now, to confirm this, we can check it online, whether the address is valid or not. I went to blockchair.com and pasted the probable address to search:

### Wallet Address Confirmation

Luckily, it was the correct one. Now, we need to research further to get the link of this address and the threat group involved.

We can see the transaction history of that wallet. When one will go on to each one, he/she can check for the further details like privacy checks.

When I went down on the last transaction received:

### Last Transaction

Inside that transaction history and from there, inside the privacy check:

### Privacy Check

We can see the details of transactions, like the sender and receiver:

### Transaction’s Privacy Details

Just copy the sender’s address and search on Google:

### Google Search for Sender

One can visit any link further. (Even I visited some). But, the third one in the list has something juicy:

### Threat Group Reveal

Finally, it shows the link of LockBit Ransomware Group with this wallet.