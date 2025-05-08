# AV Vendors

Many AV vendors in the market focus on implementing security products for home or enterprise users. Modern AV software combines antivirus capabilities with other security features such as firewalls, encryption, anti-spam, EDR, vulnerability scanning, VPNs, etc.

It is hard to recommend the best AV software as it depends on user preferences and experience. Nowadays, AV vendors also focus on business security. For more details on enterprise AV vendors, check the [AV Comparatives website](https://www.av-comparatives.org/).

---

## AV Testing Environment

AV testing environments are useful for analyzing suspicious or malicious files. Platforms like VirusTotal scan files against various AV software vendors and provide results within seconds. As a red teamer or pentester, testing payloads against well-known AV applications is essential to evaluate bypass techniques.

### VirusTotal

[VirusTotal](https://www.virustotal.com/) is a popular web-based scanning platform that checks files using over 70 antivirus detection engines. It performs various checks, including:

- Blacklisted URLs or services
- Signatures
- Binary and behavioral analysis
- API calls

Files are also executed in a simulated, isolated environment for better results.

#### VirusTotal Alternatives

**Important Note:** VirusTotal shares scanned results with antivirus vendors to improve their databases. This can expose payloads used in engagements. Alternatives without sharing policies include:

- **[AntiscanMe](https://antiscan.me/)**: 6 free scans per day
- **[Jotti's Malware Scan](https://virusscan.jotti.org/)**

These alternatives may have limitations, such as a restricted number of daily scans unless subscribed.

---

## Fingerprinting AV Software

When gaining initial access to a target machine, identifying installed AV software is crucial. AV fingerprinting helps determine the AV vendor, enabling the creation of a similar environment for testing bypass techniques.

### Common AV Software

| Antivirus Name       | Service Name(s)                  | Process Name(s)               |
|-----------------------|----------------------------------|--------------------------------|
| Microsoft Defender    | WinDefend                       | MSMpEng.exe                   |
| Trend Micro           | TMBMSRV                         | TMBMSRV.exe                   |
| Avira                 | AntivirService, Avira.ServiceHost | avguard.exe, Avira.ServiceHost.exe |
| Bitdefender           | VSSERV                          | bdagent.exe, vsserv.exe       |
| Kaspersky             | AVP<Version #>                  | avp.exe, ksde.exe             |
| AVG                   | AVG Antivirus                   | AVGSvc.exe                    |
| Norton                | Norton Security                 | NortonSecurity.exe            |
| McAfee                | McAPExe, Mfemms                 | MCAPExe.exe, mfemms.exe       |
| Panda                 | PavPrSvr                        | PavPrSvr.exe                  |
| Avast                 | Avast Antivirus                 | afwServ.exe, AvastSvc.exe     |

---

### SharpEDRChecker

**SharpEDRChecker** is a public tool written in C# for fingerprinting AV software. It checks:

- Running processes
- File metadata
- Loaded DLLs
- Registry keys
- Services
- Directories and files

#### Usage Instructions

1. Open the **SharpEDRChecker** project in Microsoft Visual Studio 2022.
2. Compile the project.
3. Locate the compiled version in the output section.
4. Run the tool via the command prompt:

    ```cmd
    C:\> SharpEDRChecker.exe
    ```

**Example Output:**

```
[!] Directory Summary:
    [-] C:\Program Files\Windows Defender : defender
    [-] C:\Program Files\Windows Defender Advanced Threat Protection : defender, threat

[!] Service Summary:
    [-] WinDefend : antimalware, defender, malware, msmpeng
```

Note: This tool may be flagged as malicious by AV software due to its checks and API calls.

---

### C# Fingerprint Checks

You can create a custom C# program to enumerate AV software. Below is an example:

```csharp
using System;
using System.Management;

internal class Program
{
     static void Main(string[] args)
     {
          var status = false;
          Console.WriteLine("[+] Antivirus check is running .. ");
          string[] AV_Check = { 
                "MsMpEng.exe", "AdAwareService.exe", "afwServ.exe", "avguard.exe", "AVGSvc.exe", 
                "bdagent.exe", "BullGuardCore.exe", "ekrn.exe", "fshoster32.exe", "GDScan.exe", 
                "avp.exe", "K7CrvSvc.exe", "McAPExe.exe", "NortonSecurity.exe", "PavFnSvr.exe", 
                "SavService.exe", "EnterpriseService.exe", "WRSA.exe", "ZAPrivacyService.exe" 
          };
          var searcher = new ManagementObjectSearcher("select * from win32_process");
          var processList = searcher.Get();
          foreach (var process in processList)
          {
                if (Array.IndexOf(AV_Check, process["Name"].ToString()) > -1)
                {
                     Console.WriteLine("--AV Found: {0}", process["Name"].ToString());
                     status = true;
                }
          }
          if (!status) { Console.WriteLine("--AV software is not found!"); }
     }
}
```

#### Explanation

- **AV_Check Array:** Contains a list of known AV processes.
- **WMIC Query:** Retrieves running processes using `win32_process`.
- **Comparison:** Matches running processes with the predefined list.

#### Compilation and Testing

1. Compile the program in Visual Studio 2022.
2. Upload the compiled file to VirusTotal for analysis.

**Note:** VirusTotal may flag the program as malicious due to poorly implemented detection methods by some AV vendors.

---

### VirusTotal Results

- **x86 Compilation:** Flagged by 2 AV vendors (MaxSecure, SecureAge APEX).
- **x64 Compilation:** Flagged by 3 AV vendors (Cyren added).

These false positives may result from machine-learning classifiers or rule-based detection methods.

For more details, refer to the submission reports on VirusTotal.