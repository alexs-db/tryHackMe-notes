Much of Linux privilege controls rely on controlling the users and files interactions. This is done with permissions. By now, you know that files can have read, write, and execute permissions. These are given to users within their privilege levels. This changes with SUID (Set-user Identification) and SGID (Set-group Identification). These allow files to be executed with the permission level of the file owner or the group owner, respectively.

You will notice these files have an “s” bit set showing their special permission level.

```bash
find / -type f -perm -04000 -ls 2>/dev/null
```

This command will list files that have SUID or SGID bits set.

A good practice would be to compare executables on this list with [GTFOBins](https://gtfobins.github.io). Clicking on the SUID button will filter binaries known to be exploitable when the SUID bit is set (you can also use this [pre-filtered list](https://gtfobins.github.io/#+suid)).

The list above shows that `nano` has the SUID bit set. Unfortunately, GTFObins does not provide us with an easy win. Typical to real-life privilege escalation scenarios, we will need to find intermediate steps that will help us leverage whatever minuscule finding we have.

> **Note:** The attached VM has another binary with SUID other than `nano`.

The SUID bit set for the `nano` text editor allows us to create, edit and read files using the file owner’s privilege. `Nano` is owned by root, which probably means that we can read and edit files at a higher privilege level than our current user has. At this stage, we have two basic options for privilege escalation: reading the `/etc/shadow` file or adding our user to `/etc/passwd`.

Below are simple steps using both vectors.

### Reading the `/etc/shadow` file

We see that the `nano` text editor has the SUID bit set by running the `find / -type f -perm -04000 -ls 2>/dev/null` command.

```bash
nano /etc/shadow
```

This will print the contents of the `/etc/shadow` file. We can now use the `unshadow` tool to create a file crackable by John the Ripper. To achieve this, `unshadow` needs both the `/etc/shadow` and `/etc/passwd` files.

The `unshadow` tool’s usage can be seen below:

```bash
unshadow passwd.txt shadow.txt > passwords.txt
```

With the correct wordlist and a little luck, John the Ripper can return one or several passwords in cleartext. For a more detailed room on John the Ripper, you can visit [this TryHackMe room](https://tryhackme.com/room/johntheripper0).

### Adding a new user to `/etc/passwd`

The other option would be to add a new user that has root privileges. This would help us circumvent the tedious process of password cracking. Below is an easy way to do it:

We will need the hash value of the password we want the new user to have. This can be done quickly using the `openssl` tool on Kali Linux.

We will then add this password with a username to the `/etc/passwd` file.

Once our user is added (please note how `root:/bin/bash` was used to provide a root shell) we will need to switch to this user and hopefully should have root privileges.