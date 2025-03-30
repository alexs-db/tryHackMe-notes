# Host Security Solutions

Before performing further actions, we need to obtain general knowledge about the security solutions in place. It is important to enumerate antivirus and security detection methods on an endpoint to stay as undetected as possible and reduce the chance of getting caught.

This task discusses common security solutions used in corporate networks, divided into **Host** and **Network** security solutions.

## Host Security Solutions

Host security solutions are a set of software applications used to monitor and detect abnormal and malicious activities within the host. These include:

- Antivirus software
- Microsoft Windows Defender
- Host-based Firewall
- Security Event Logging and Monitoring
- Host-based Intrusion Detection System (HIDS)/Host-based Intrusion Prevention System (HIPS)
- Endpoint Detection and Response (EDR)

Let’s explore these host-based security solutions in detail.

---

### Antivirus Software (AV)

Antivirus software, also known as anti-malware, is used to monitor, detect, and prevent malicious software from being executed within the host. Common features include:

- **Background scanning**: Real-time scanning of open and used files.
- **Full system scans**: Essential when first installing the antivirus.
- **Virus definitions**: Pre-defined signatures of known viruses, requiring regular updates.

#### Detection Techniques

1. **Signature-based detection**: Compares scanned files with a database of known signatures.
2. **Heuristic-based detection**: Uses machine learning to analyze suspicious properties in application code.
3. **Behavior-based detection**: Monitors application execution for abnormal behaviors, such as registry changes or process manipulation.

#### Enumeration of Antivirus Software

You can enumerate antivirus software using Windows built-in tools like `wmic` or PowerShell:

```powershell
# Using WMIC
wmic /namespace:\\root\securitycenter2 path antivirusproduct

# Using PowerShell
Get-CimInstance -Namespace root/SecurityCenter2 -ClassName AntivirusProduct
```

Example output:

```plaintext
displayName              : Bitdefender Antivirus
instanceGuid             : {BAF124F4-FA00-8560-3FDE-6C380446AEFB}
pathToSignedProductExe   : C:\Program Files\Bitdefender\Bitdefender Security\wscfix.exe
pathToSignedReportingExe : C:\Program Files\Bitdefender\Bitdefender Security\bdservicehost.exe
productState             : 266240
timestamp                : Wed, 15 Dec 2021 12:40:10 GMT
```

---

### Microsoft Windows Defender

Microsoft Windows Defender is a pre-installed antivirus tool that uses machine learning, big-data analysis, and Microsoft cloud infrastructure for malware protection. It operates in three modes:

1. **Active mode**: Runs as the primary antivirus.
2. **Passive mode**: Functions as secondary antivirus when a third-party antivirus is installed.
3. **Disabled mode**: When Windows Defender is turned off or uninstalled.

#### Checking Windows Defender Status

Use the following PowerShell commands:

```powershell
# Check service state
Get-Service WinDefend

# Check real-time protection status
Get-MpComputerStatus | select RealTimeProtectionEnabled
```

---

### Host-based Firewall

A host-based firewall controls inbound and outbound traffic through the device's interface, protecting the host from untrusted devices on the same network. Modern firewalls analyze traffic at multiple levels, including packet analysis and application-layer inspection.

#### Enumerating Firewall Rules

Use PowerShell to check firewall profiles and rules:

```powershell
# Check firewall profiles
Get-NetFirewallProfile | Format-Table Name, Enabled

# Disable firewall profiles (requires admin privileges)
Set-NetFirewallProfile -Profile Domain, Public, Private -Enabled False

# Check firewall rules
Get-NetFirewallRule | select DisplayName, Enabled, Description
```

#### Testing Firewall Connections

You can test inbound connections using PowerShell:

```powershell
# Test connection on port 80
Test-NetConnection -ComputerName 127.0.0.1 -Port 80

# Alternative method
(New-Object System.Net.Sockets.TcpClient("127.0.0.1", "80")).Connected
```

Example output:

```plaintext
ComputerName     : 127.0.0.1
RemoteAddress    : 127.0.0.1
RemotePort       : 80
TcpTestSucceeded : True
```

This confirms that port 80 is open and allowed by the firewall.