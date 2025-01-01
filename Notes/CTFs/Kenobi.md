[Task 1] Deploy the vulnerable machine

# NMAP SCAN

```bash
nmap -A -sC -sV 10.10.28.128
```

## Scan the machine with nmap, how many ports are open?

Answer: 4

[Task 2] Enumerating Samba for shares

## Using the nmap command above, how many shares have been found?

Answer: 3

```bash
sudo nmap -p 445 --script=smb-enum-shares.nse,smb-enum-users.nse 10.10.28.178
```

Alternatively, we can use `smbclient` to list shares of the target machine.

## Once you’re connected, list the files on the share. What is the file you can see?

Answer: log.txt

## What port is FTP running on?

Answer: 21

## What mount can we see?

Answer: /var

```bash
sudo nmap -p 111 --script=nfs-ls,nfs-statfs,nfs-showmount 10.10.28.178
```

[Task 3] Gain initial access with ProFtpd

This machine is using Proftpd FTP Server.

## Let's get the version of ProFtpd. Use netcat to connect to the machine on the FTP port. What is the version?

Answer: 1.3.5

Now that we know the name of the service being used and its version, let's use `Searchsploit` to check Exploit-DB to see if there are any known vulnerabilities for this exact version.

## How many exploits are there for the ProFTPd running?

Answer: 4

Looking at the results, we can see that there are indeed vulnerabilities for this version of the service. On further inspection, we can see that they all involve the copy command. We see a module named `mod_copy` that allows running remote commands. Let's take a look at the manual for this function and see how we can use it.

The `mod_copy` module implements `SITE CPFR` and `SITE CPTO` commands, which can be used to copy files/directories from one place to another on the server. Any unauthenticated client can leverage these commands to copy files from any part of the filesystem to a chosen destination.

We know that the FTP service is running as the Kenobi user (from the file on the share) and an SSH key is generated for that user.

We’re now going to copy Kenobi’s private key using `SITE CPFR` and `SITE CPTO` commands.

We knew that the `/var` directory was a mount we could see (task 2, question 4).

So we’ve now moved Kenobi’s private key to the `/var/tmp` directory.

Let's mount the `/var/tmp` directory to our machine.

We now have a network mount on our deployed machine! We can go to `/var/tmp` and get the private key, then log in to Kenobi’s account.

Copy the `id_rsa` file to your Kenobi folder.

Change the access permissions of file system objects.

Now that we have copied the SSH key onto our system, let's use SSH and log in to the target system and find the user flag.

## What is Kenobi’s user flag (/home/kenobi/user.txt)?

Answer: d0b0f3f53b6caa532a83915e19224899

[Task 4] Privilege Escalation with Path Variable Manipulation

SUID bits can be dangerous. Some binaries, such as `passwd`, need to be run with elevated privileges (as it's resetting your password on the system). However, other custom files that have the SUID bit can lead to all sorts of issues.

To search the system for these types of files, run the following:

```bash
find / -perm -u=s -type f 2>/dev/null
```

## What file looks particularly out of the ordinary?

Answer: /usr/bin/menu

## Run the binary, how many options appear?

Answer: 3

`Strings` is a command on Linux that looks for human-readable strings in a binary.

When we look at the results, we see `curl` (a utility found on Linux) is being used in one of the steps during execution. The binary for `curl` has been directly invoked without using the complete path to the binary. When we don’t specify the complete path, the system will look at the `PATH` variable and search in all the locations that are specified in the path for the file that is required. Since the `PATH` variable is editable by anyone, we can use this to our advantage.

This shows us the binary is running without a full path (e.g., not using `/usr/bin/curl` or `/usr/bin/uname`).

As this file runs with root user privileges, we can manipulate our path to gain a root shell.

## What is the root flag (/root/root.txt)?

Answer: 177b3cd8562289f37382721c28381f02