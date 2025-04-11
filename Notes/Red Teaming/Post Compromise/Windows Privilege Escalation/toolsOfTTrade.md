# Tools for Windows Privilege Escalation

Several scripts exist to conduct system enumeration in ways similar to the ones seen in the previous task. These tools can shorten the enumeration process time and uncover different potential privilege escalation vectors. However, please remember that automated tools can sometimes miss privilege escalation opportunities.

Below are a few tools commonly used to identify privilege escalation vectors. Feel free to run them against any of the machines in this room and see if the results match the discussed attack vectors.

---

## WinPEAS

**WinPEAS** is a script developed to enumerate the target system to uncover privilege escalation paths. It runs commands similar to the ones listed in the previous task and prints their output. The output from WinPEAS can be lengthy and sometimes difficult to read. It is good practice to redirect the output to a file, as shown below:

```cmd
C:\> winpeas.exe > outputfile.txt
```

WinPEAS can be downloaded [here](https://github.com/carlospolop/PEASS-ng).

---

## PrivescCheck

**PrivescCheck** is a PowerShell script that searches for common privilege escalation vectors on the target system. It provides an alternative to WinPEAS without requiring the execution of a binary file.

PrivescCheck can be downloaded [here](https://github.com/itm4n/PrivescCheck).

To run PrivescCheck on the target system, you may need to bypass the execution policy restrictions. Use the `Set-ExecutionPolicy` cmdlet as shown below:

```powershell
PS C:\> Set-ExecutionPolicy Bypass -Scope Process -Force
PS C:\> . .\PrivescCheck.ps1
PS C:\> Invoke-PrivescCheck
```

---

## WES-NG: Windows Exploit Suggester - Next Generation

Some exploit-suggesting scripts (e.g., WinPEAS) require uploading them to the target system, which may trigger antivirus detection. To avoid unnecessary noise, you can use **WES-NG**, which runs on your attacking machine (e.g., Kali or TryHackMe AttackBox).

WES-NG is a Python script that can be downloaded [here](https://github.com/bitsadmin/wesng).

### Steps to Use WES-NG:
1. Install WES-NG and update its database:
    ```bash
    user@kali$ wes.py --update
    ```
2. Run the `systeminfo` command on the target system and save the output to a `.txt` file.
3. Transfer the `.txt` file to your attacking machine.
4. Run WES-NG with the following command:
    ```bash
    user@kali$ wes.py systeminfo.txt
    ```

---

## Metasploit

If you already have a Meterpreter shell on the target system, you can use the `multi/recon/local_exploit_suggester` module to list vulnerabilities that may allow privilege escalation.

```bash
meterpreter > use post/multi/recon/local_exploit_suggester
```