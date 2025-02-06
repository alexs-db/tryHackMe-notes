# Introduction

## Deploy the machine!
**answer:** No answer needed

# Challenges

## Enter the key you found!
**answer:** [NO SPOILER]

## nmap kicking off!

```bash
$ nmap -sV -sC $TARGET
```

Among the ports, one stood out (113)! It said:

```
|_ http://localhost/key_rev_key <- You will find the key here!!!
```

So I tried to hit the URL `http://10.10.172.75/key_rev_key`, and it downloaded a file.

When I ran `cat` on the file, it showed me some buffers, so I ran `strings` on it to try printable characters.

```bash
$ strings key_rev_key
```

Found both the username and key!

## What is Charlie’s password?
**answer:** [NO SPOILER]

Saw that port 80 is open. So I tried to run `ffuf` on it.

```bash
$ ffuf -v -c -recursion -t 64 -e $EXTS -w $W_COMMON -u http://$TARGET/FUZZ
```

Found the `home.php` and `index.html`.

`home.php`

`index.html`

Looks like `home.php` is more interesting since I can run commands on it!

I tried a few commands and found out that listing the home directory was very fruitful!

`teleport` and `teleport.pub` (ssh) are readable by everyone?! So I `cat` it out:

And made it into `id_rsa` file, `chmod` to 600, and logged in all the way.

## Enter the user flag
**answer:** [NO SPOILER]

Since we already knew the `user.txt` is in `/home/charlie`, it’s just a `cat` away.

## Enter the root flag
**answer:** [NO SPOILER]

I tried to check my sudo permission

```bash
$ sudo -l
```

and found out I have `vi` permission.

After going into `vi`, I ran `: + sh` to get the shell.

Checked the `/root` folder and found out there is a Python file `root.py`, tried to run it and put in the key found above, and Bingo!

Till now we still do not know Charlie’s password!

Since I am root, I got the hash from `/etc/shadow` and created a local file `shadow`.

And ran through `john`:

```bash
$ john shadow
```

Looking at the password input. The format is XXXXXX (input text box), it has 6 characters! But `john` found 2232, which has only 4?!!

I spent some time looking around and ended up peeping at the given writeup. It was the web login password it was asking!

So I ran a `cat` on the `validate.php` file and got the answer.