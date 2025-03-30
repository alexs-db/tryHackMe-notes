# Host Security Solutions

## Security Event Logging and Monitoring

Operating systems log various activity events in the system using log files by default. This event logging feature allows IT system and network administrators to monitor and analyze important events on both the host and network sides. In corporate networks, security teams use event logging to track and investigate security incidents.

Windows operating systems categorize event logs into areas such as applications, systems, security, and services. Security and network devices also store event information in log files, enabling system administrators to gain insights into system activities.

To list available event logs on a local machine, use the `Get-EventLog` cmdlet in PowerShell:

```powershell
PS C:\Users\thm> Get-EventLog -List

    Max(K) Retain OverflowAction        Entries Log
    ------ ------ --------------        ------- ---
         512      7 OverwriteOlder             59 Active Directory Web Services
    20,480      0 OverwriteAsNeeded         512 Application
         512      0 OverwriteAsNeeded         170 Directory Service
 102,400      0 OverwriteAsNeeded          67 DNS Server
    20,480      0 OverwriteAsNeeded       4,345 System
    15,360      0 OverwriteAsNeeded       1,692 Windows PowerShell
```

The list of event logs can reveal installed applications and services, such as Active Directory or DNS Server. For more details on the `Get-EventLog` cmdlet, refer to the [Microsoft documentation](https://learn.microsoft.com/).

In corporate environments, log agent software is often installed on clients to collect logs from various sensors for analysis and monitoring. This will be discussed further in the Network Security Solution task.

---

## System Monitor (Sysmon)

Windows System Monitor (Sysmon) is a service and device driver included in the Microsoft Sysinternals suite. Sysmon is not installed by default but starts logging events once installed. It is a valuable tool for system administrators and blue teams to track malicious activity and troubleshoot issues.

Sysmon can log events such as:
- Process creation and termination
- Network connections
- File modifications
- Remote threats
- Process and memory access

For more information, visit the [Windows Sysmon documentation](https://learn.microsoft.com/).

### Detecting Sysmon on a Machine

To check if Sysmon is installed, you can:
1. Look for a process or service named "Sysmon":
     ```powershell
     PS C:\Users\thm> Get-Process | Where-Object { $_.ProcessName -eq "Sysmon" }
     PS C:\Users\thm> Get-CimInstance win32_service -Filter "Description = 'System Monitor service'"
     PS C:\Users\thm> Get-Service | Where-Object { $_.DisplayName -like "*sysm*" }
     ```

2. Check the Windows registry:
     ```powershell
     PS C:\Users\thm> reg query HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\WINEVT\Channels\Microsoft-Windows-Sysmon/Operational
     ```

3. Search for the Sysmon configuration file:
     ```powershell
     PS C:\Users\thm> findstr /si '<ProcessCreate onmatch="exclude">' C:\tools\*
     ```

For more details, explore the TryHackMe room: **Sysmon**.

---

## Host-based Intrusion Detection/Prevention System (HIDS/HIPS)

### HIDS
Host-based Intrusion Detection Systems (HIDS) monitor and detect abnormal or malicious activities on a host. HIDS primarily detects suspicious activities but does not prevent them. Detection methods include:
- **Signature-based IDS**: Checks checksums and message authentication.
- **Anomaly-based IDS**: Detects unexpected activities like abnormal bandwidth usage or unusual protocols and ports.

### HIPS
Host-based Intrusion Prevention Systems (HIPS) secure operating system activities on the device where they are installed. HIPS combines features such as antivirus, behavior analysis, and application firewalls to detect and prevent attacks.

Network-based IDS/IPS will be covered in the next task.

---

## Endpoint Detection and Response (EDR)

Endpoint Detection and Response (EDR), also known as Endpoint Detection and Threat Response (EDTR), is a cybersecurity solution that defends against malware and other threats. EDR monitors endpoints, system, and network events, recording them for analysis and investigation. It is considered the next generation of antivirus.

### EDR Capabilities
EDR detects threats such as:
- Malware (e.g., viruses, trojans, adware, keyloggers)
- Exploit chains
- Ransomware

### Common EDR Software
- Cylance
- CrowdStrike
- Symantec
- SentinelOne

Even if an attacker bypasses EDR to gain a reverse shell, EDR continues monitoring the system and may block further actions if flagged.

### Enumerating Security Products
Scripts like `Invoke-EDRChecker` and `SharpEDRChecker` can enumerate security products on a machine by checking:
- File metadata
- Processes
- DLLs loaded into processes
- Services and drivers
- Directories