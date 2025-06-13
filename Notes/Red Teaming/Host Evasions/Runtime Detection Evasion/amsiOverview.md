# AMSI (Anti-Malware Scan Interface) Overview

AMSI (Anti-Malware Scan Interface) is a PowerShell security feature that allows applications and services to integrate directly with anti-malware products. Microsoft Defender uses AMSI to scan payloads and scripts before execution inside the .NET runtime.

> From Microsoft:  
> "The Windows Antimalware Scan Interface (AMSI) is a versatile interface standard that allows your applications and services to integrate with any anti-malware product that's present on a machine. AMSI provides enhanced malware protection for your end-users and their data, applications, and workloads."

For more information about AMSI, see the [Windows documentation](https://docs.microsoft.com/en-us/windows/win32/amsi/antimalware-scan-interface-portal).

## AMSI Response Codes

AMSI determines its actions based on response codes returned after monitoring and scanning. Below are the possible response codes:

| Response Code                        | Value    |
|---------------------------------------|----------|
| `AMSI_RESULT_CLEAN`                   | 0        |
| `AMSI_RESULT_NOT_DETECTED`            | 1        |
| `AMSI_RESULT_BLOCKED_BY_ADMIN_START`  | 16384    |
| `AMSI_RESULT_BLOCKED_BY_ADMIN_END`    | 20479    |
| `AMSI_RESULT_DETECTED`                | 32768    |

These codes are reported on the backend of AMSI or through third-party implementations. If AMSI detects malicious content, it halts execution and displays an error message similar to the following:

```
AMSI
Error Response
PS C:\Users\Tryhackme> 'Invoke-Hacks'
At line:1 char:1
+ "Invoke-Hacks"
+ ~~~~~~~~~~~~~~
This script contains malicious content and has been blocked by your antivirus software.
    + CategoryInfo          : ParserError: (:) []. ParentContainsErrorRecordException
    + FullyQualifiedErrorId : ScriptContainedMaliciousContent
```

## Windows Components Integrated with AMSI

AMSI is fully integrated into the following Windows components:

- User Account Control (UAC)
- PowerShell
- Windows Script Host (wscript and cscript)
- JavaScript and VBScript
- Office VBA macros

When targeting these components, attackers must be mindful of AMSI and its implementations when executing code or abusing components.

---

In the next task, we will cover the technical details behind how AMSI works and is instrumented in Windows.