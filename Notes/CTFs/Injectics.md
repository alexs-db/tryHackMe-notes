Let’s start Nmap to enumerate the open ports.

```bash
sudo nmap -A 10.10.34.78 -T5 -o Init_scan.txt
```

**Command Breakdown:**

- `sudo`: Provides the command root privileges.
- `-A`: Shorthand for several options such as OS Detection, Version Detection, Script Scanning, Traceroute, and Aggressive Timing.
- `-T5`: Assigns aggressive timing template for faster scans.
- `-o`: Allows saving the output in .txt format for future reference. Other formats are also available.

Nothing special, just ports 22 and 80. Let’s start Gobuster for directory enumeration while we manually evaluate the website.

```bash
gobuster dir -u 10.10.34.78 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x html,js,txt,php,db,json,log -k 2>/dev/null
```

**Command Breakdown:**

- `-u`: Specifies the IP/domain to target.
- `-w`: The word list to be used, I’m using `directory-list-2.3-medium.txt` from the seclists word list.
- `-t`: The threads to be used. The more threads, the faster it will run.
- `-x`: To find, for example, ‘vulnerable’ PHP pages or files like TXT, adds a suffix to the end of the words in the word list. While progressing through different CTFs, I try to append the extensions so I don’t lose important information.
- `2>/dev/null`: This redirects all errors to `/dev/null`, effectively removing unnecessary noise from the results.

While exploring, it seems that all the buttons are dummy except for the login button in the top right.

While there are a few things we could try on the login page, let’s not waste time with random attacks. First, let’s check the progress on Gobuster. We see `/mail.log`. It seems interesting, so let’s give it a look.

It seems to provide us with some credentials. Time to test them:

- `superadmin@injectics.thm` with password `superSecurePasswd101`
- `dev@injectics.thm` with password `devPasswd123`

Hmm, they don’t work.

After reading again, it states these credentials will work only if the users table is accidentally deleted. So, to my understanding, we need to find a way to drop the users table to make our credentials valid.

Let’s go back to the login page and try to find an injection point.

Tried using SQLmap, but after a lot of time, it did not work. So I’ll try another approach. We’ll capture the request and use an SQLi word list.

Now let’s send it to Intruder to start testing the parameters.

Clear all parameters except the username. To do so, use the buttons on the right, as shown in the image below.

**Informational:**

The username field is often targeted in SQLi attacks because it can reveal more about the database structure, facilitate authentication bypass, and allow for easier manipulation of queries compared to the password field.

Since I don’t have any SQLi payloads, let’s download one.

[sql-injection-payload-list/Intruder/exploit/Auth_Bypass.txt at master ·…](https://github.com/payloadbox/sql-injection-payload-list)

Load the payload to the Intruder.

And start the attack.

All of them produce a 200 status, so we’ll be looking for a different length that could indicate success.

The payload loaded is for authentication bypass. This is because I used SQLmap before, which tests for all types of SQLi but doesn’t do a good job with authentication bypass.

Okay, one seems to stand out with a length of 451.

Let’s check its response.

Let’s copy the payload.

```sql
' OR 'x'='x'#
```

And go to Repeater and manually change the username parameter.

Send it and open the response in the browser.

Now, by refreshing the page, you should be logged in. If you are using FoxyProxy in your browser, turn it off and refresh the page.

**What is FoxyProxy:**

FoxyProxy is a web extension that routes web traffic through proxy servers, enabling you to use Burp Suite with your preferred browser instead of Burp’s default Chromium.

Now, there is a table that I believe is connected to the database, so let’s try to drop the table and make the credentials we found valid.

After searching, I found this:

```sql
DROP TABLE table_name;
```

Now remember that the table that should be dropped according to the message we found earlier is `users`. Also, since spaces cannot be interpreted for the command to work, we added `+` instead.

Click on Edit and then capture the request. After the gold parameter value, add `;` to continue with the command, which looks like this:

```sql
DROP+TABLE+users;
```

Now let's send it.

It gives a 302. Not sure if it worked, so let’s try the credentials and find out.

If you switch off your proxy and log out, you’ll get this.

Even if you don’t get the above message, it might still have worked. Proceed with the next steps to verify if it worked.

Back to the login page.

**Credentials for your convenience:**

- `superadmin@injectics.thm` with password `superSecurePasswd101`

On the login page, click on “Login as Admin.”

And it worked!

We got the first flag! Time to answer the question.

**What is the flag value after logging into the admin panel?**

`THM{INJECTICS_ADMIN_PANEL_007}`

From that point, the only new endpoint is the profile on the top right.

I guess there is something to do here, but I’m not sure what. Let’s get back to enumerating. We left Gobuster running but haven’t checked what it found, so let’s give it a look.

Going through them, in the `/composer.json` there seems to be something promising.

It provides that PHP is using Twig, which is a template engine. From a little research, I found that Twig is vulnerable to Server-Side Template Injection (SSTI).

**Informational:**

Server-Side Template Injection (SSTI) allows an attacker to inject and execute arbitrary code on the server by manipulating the template engine’s syntax.

Let’s try to check if there is an SSTI vulnerability on the “Update Profile” page.

We need to find a way to get visual results.

On the top left, we can see “admin.”

And in the “Update Profile” section, the first name is “admin.”

Now, let’s see if changing the first name parameter in the “Update Profile” section will also update it on the home page.

It seems like we found our visual injection point.

Now it’s time to test for an SSTI vulnerability. To do so, we can use a simple command and see if it gets executed.

```twig
{{7+7}}
```

We have just validated that there is an SSTI. Now we need to use it in such a way to gain access to the server.

The following resource can help us try different payloads and understand SSTI better:

[SSTI (Server Side Template Injection) | HackTricks](https://book.hacktricks.xyz/pentesting-web/ssti-server-side-template-injection)

I tried hard to get a reverse shell but without any luck. Since we have a visible point and can see through the machine, let’s try to simply read the flag.

**The question is:**

What is the content of the hidden text file in the flags folder?

We know the flag is in the flags folder.

Now, let’s capture the request of the “Update Profile” page and send it to Repeater to make the process a bit faster than going back and forth on the page.

When making the request, by default, it’s trying to URL-encode our payload. Delete the URL encoding shown in the below first request and send it as-is on the second request.

The command to use is:

```twig
{{['ls',""]|sort('passthru')}}
```

**Command Breakdown:**

- `[ls, ]`: An array with the `ls` command and an empty string. The `ls` command lists directory contents.
- `|sort('passthru')`: The `|` symbol pipes the output of `[ls, ]` to the sort function. Using `passthru` with sort indicates that the command’s output is executed and displayed directly, without additional processing.

**Informational:**

The vulnerability is SSTI that leads to Remote Code Execution (RCE).

Send it, and as the image below shows, we can see the flags folder.

Now let's change to folder flags

```twig
{{['cd+flags;ls',""]|sort('passthru')}}
```

And open the file `5d8af1dc14503c7e4bdc8e51a3469f48.txt`

```twig
{{['cat+flags/5d8af1dc14503c7e4bdc8e51a3469f48.txt;ls',""]|sort('passthru')}}
```

And we got our final flag!

**What is the content of the hidden text file in the flags folder?**

`THM{5735172b6c147f4dd649872f73e0fdea}`
