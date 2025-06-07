# GUI-Based UAC Bypasses

We will start by looking at GUI-based bypasses, as they provide an easy way to understand the basic concepts involved. These examples are not usually applicable to real-world scenarios, as they rely on having access to a graphical session, from where we could use the standard UAC to elevate.

> **Deploy the VM:**  
> Click the **Start Machine** button to deploy your VM and connect to it via RDP or in the side-by-side view in Browser:
>
> ```bash
> xfreerdp /v:MACHINE_IP /u:attacker /p:Password321
> ```
>
> This machine will be used for all tasks in the room.

---

## Case Study: msconfig

Our goal is to obtain access to a High IL (Integrity Level) command prompt without passing through UAC. First, open **msconfig** from the Start menu or the "Run" dialog.

If we analyze the msconfig process with **Process Hacker** (available on your desktop), we notice something interesting: even when no UAC prompt was presented, msconfig runs as a high IL process.

This is possible thanks to a feature called **auto elevation** that allows specific binaries to elevate without requiring user interaction.

If we could force msconfig to spawn a shell for us, the shell would inherit the same access token and therefore run as a high IL process. By navigating to the **Tools** tab, we can find an option to do just that.

If we click **Launch**, we will obtain a high IL command prompt without interacting with UAC in any way.

**To retrieve the msconfig flag, use the obtained high integrity console to execute:**

```cmd
C:\> C:\flags\GetFlag-msconfig.exe
```

---

## Case Study: azman.msc

As with msconfig, **azman.msc** will auto elevate without requiring user interaction. If we can find a way to spawn a shell from within that process, we will bypass UAC. Unlike msconfig, azman.msc has no intended built-in way to spawn a shell, but we can overcome this with a bit of creativity.

1. **Run azman.msc.**
2. Confirm that a process with high IL was spawned using **Process Hacker**. Notice that all `.msc` files are run from **mmc.exe** (Microsoft Management Console).
3. To run a shell, abuse the application's help:
    - On the help screen, right-click any part of the help article and select **View Source**.
    - This will spawn a **notepad** process that we can leverage to get a shell.
    - Go to **File → Open** in Notepad, select **All Files** in the combo box, navigate to `C:\Windows\System32`, find `cmd.exe`, right-click, and select **Open**.

This will bypass UAC and give us access to a high integrity command prompt. You can check the process tree in Process Hacker to see how the high integrity token is passed from mmc (launched through Azman) all the way to cmd.exe.

**To retrieve the azman flag, use the obtained high integrity console to execute:**

```cmd
C:\> C:\flags\GetFlag-azman.exe
```
