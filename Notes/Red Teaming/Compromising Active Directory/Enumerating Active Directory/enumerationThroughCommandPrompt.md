# Enumerating Active Directory via Command Prompt

The Command Prompt (`cmd.exe`) can be a valuable tool for quick Active Directory (AD) lookups, especially when:

- You lack RDP access to a system.
- Defenders are monitoring for PowerShell usage.
- You need to enumerate AD through a Remote Access Trojan (RAT).
- You want to embed simple AD enumeration commands in a phishing payload.

`cmd.exe` includes the built-in `net` command, which allows enumeration of both local system and AD information. Below are some common enumeration tasks using `net`. (This is not an exhaustive list.)

> **Note:** For this task, you must use `THMJMP1` and not your own Windows VM. Details are explained in the drawbacks section.

---

## Enumerating Users

List all users in the AD domain:

```cmd
net user /domain
```

Sample output:
```
The request will be processed at a domain controller for domain za.tryhackme.com

User accounts for \\THMDC

-------------------------------------------------------------------------------
aaron.conway             aaron.hancock            aaron.harris
aaron.johnson            aaron.lewis              aaron.moore
...
The command completed successfully.
```

Get detailed information about a specific user:

```cmd
net user zoe.marshall /domain
```

Sample output:
```
User name                    zoe.marshall
Full Name                    Zoe Marshall
...
Global Group memberships     *Domain Users         *Internet Access
The command completed successfully.
```

> **Note:** If a user is a member of more than ten groups, not all memberships may be listed.

---

## Enumerating Groups

List all groups in the domain:

```cmd
net group /domain
```

Sample output:
```
Group Accounts for \\THMDC

-------------------------------------------------------------------------------
*Cloneable Domain Controllers
*DnsUpdateProxy
*Domain Admins
...
The command completed successfully.
```

List members of a specific group:

```cmd
net group "Tier 1 Admins" /domain
```

Sample output:
```
Group name     Tier 1 Admins

Members

-------------------------------------------------------------------------------
t1_arthur.tyler          t1_gary.moss             t1_henry.miller
...
The command completed successfully.
```

---

## Enumerating Password Policy

View the domain password policy:

```cmd
net accounts /domain
```

Sample output:
```
Force user logoff how long after time expires?:       Never
Minimum password age (days):                          0
Maximum password age (days):                          Unlimited
...
The command completed successfully.
```

This provides information such as:

- Password history length (number of unique passwords before reuse).
- Lockout threshold and duration for incorrect password attempts.
- Minimum password length.
- Maximum password age (password rotation policy).

This information is useful for planning password spraying attacks and understanding account lockout risks.

---

## Benefits

- No additional or external tools required.
- Commands are often not monitored by Blue teams.
- No GUI required.
- Supported natively by VBScript and macro languages (useful for phishing payloads).

## Drawbacks

- Must be run from a domain-joined machine; otherwise, defaults to the `WORKGROUP` domain.
- May not display all information (e.g., users with more than ten group memberships).

---

For more options, refer to the official documentation for the `net` command. Experiment with these commands to gather information about users and groups in the AD domain.
