# What is Malware Analysis

Malware analysis is the process of examining a suspicious file to determine its behavior at both a micro level (by inspecting assembly code) and a macro level (by observing its actions on the system). This process helps Blue Teamers better understand malicious programs, aiding in the development of effective detections.

## Static vs. Dynamic Analysis

There are two primary approaches for analyzing suspicious files:

- **Static Analysis:** Involves examining the code without executing it, often using disassemblers such as IDA or Ghidra.
- **Dynamic Analysis:** Involves executing the file in a controlled environment to observe its behavior. Tools commonly used include EDR software, Sysmon, ProcMon, Process Hacker, and debuggers like OllyDbg, WinDbg, and x64Dbg.

## Introduction to Sandboxes

A creative and effective dynamic analysis method is the use of sandboxes. A sandbox is a containerized or virtualized environment designed to safely execute and monitor suspicious files. Depending on the sandbox, you can customize the operating system version, installed software, and more.

Sandboxes provide a secure way to observe what a suspicious file does before it is run on a production system. Many commercial sandboxes are deployed throughout networks, often in the following locations:

- **Firewalls**
- **Mail Servers**
- **Workstations**

Each sandbox may function differently. For example:

- A firewall sandbox may execute an email attachment to monitor network communications.
- A mail sandbox may open emails to detect embedded files that attempt to download data or steal credentials.
- A host-based antivirus sandbox may execute files to monitor for malicious behavior or system changes.

## Popular Sandbox Solutions

Some widely used sandbox products include:

- **Palo Alto Wildfire** (Firewall)
- **Proofpoint TAP** (Email Sandbox)
- **Falcon Sandbox** (EDR/Workstation)
- **MimeCast** (Email Sandbox)
- **VirusTotal** (Sample Submission Site)
- **Any.Run** (Sample Submission Site)
- **Antiscan.me** (Sample Submission Site)
- **Joe Sandbox** (Sample Submission Site)

---

In the next section, we will explore common techniques used by malware authors to evade detection and analysis.
