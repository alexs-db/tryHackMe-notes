# Introduction to Living Off the Land

**Living Off the Land** is a popular term in the red team community. It refers to the practice of using a target system's built-in tools and utilities for malicious purposes, rather than introducing external files or programs. The concept was introduced at DerbyCon3 in 2013 and has since become a widely adopted technique for evading detection.

Attackers leverage legitimate, Microsoft-signed programs, scripts, and libraries to blend in with normal system activity and bypass security controls. For example, tools like `CertUtil` can be abused to download malicious files onto a target machine.

## Categories of Living Off the Land Techniques

- **Reconnaissance**
- **File operations**
- **Arbitrary code execution**
- **Lateral movement**
- **Security product bypass**

## Learning Objectives

- Understand the concept of Living Off the Land in red team engagements.
- Learn about the LOLBAS project and its applications.
- Apply Living Off the Land techniques in red teaming scenarios.

## Prerequisites

- Basic knowledge of general hacking techniques.
- Completion of the Jr. Penetration Tester Learning Path.
- Completion of the TryHackMe Red Team Initial Access module.

---

A Windows 10 Pro machine is provided for this room. You can use the in-browser feature or connect via RDP. If connecting via RDP, deploy the AttackBox or connect to the VPN.

**Credentials:**

- **Machine IP:** `10.10.133.214`
- **Username:** `thm`
- **Password:** `TryHackM3`

**RDP Connection Example:**

```bash
xfreerdp /v:10.10.133.214 /u:thm /p:TryHackM3
```