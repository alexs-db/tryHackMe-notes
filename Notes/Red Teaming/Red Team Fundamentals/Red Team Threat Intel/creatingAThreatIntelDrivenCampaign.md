# Threat-Intel-Driven Campaign

A **threat-intel-driven campaign** will take all knowledge and topics previously covered and combine them to create a **well-planned and researched** campaign.

The **task flow** in this room follows the logical steps a red team would take to begin planning a campaign:

1. **Identify framework and general kill chain**
2. **Determine targeted adversary**
3. **Identify adversary's TTPs and IOCs**
4. **Map gathered threat intelligence to a kill chain or framework**
5. **Draft and maintain needed engagement documentation**
6. **Determine and use needed engagement resources** (tools, C2 modification, domains, etc.)

In this task, we will walk through a **red team’s thought process** from start to finish when planning a **threat-intel-driven campaign**.

## Mapping Cyber Frameworks

One of the **most challenging parts** of planning a **threat-intel-driven campaign** is mapping two different cyber frameworks.  
To simplify this process, the table below provides a basic comparison of the **Lockheed Martin Cyber Kill Chain** and the **MITRE ATT&CK** framework:

| **Cyber Kill Chain**      | **MITRE ATT&CK**                |
|--------------------------|--------------------------------|
| **Recon**               | **Reconnaissance**             |
| **Weaponization**       | **Execution**                  |
| **Delivery**            | **Initial Access**             |
| **Exploitation**        | **Initial Access**             |
| **Installation**        | **Persistence / Defense Evasion** |
| **Command & Control**   | **Command and Control**        |
| **Actions on Objectives** | **Exfiltration / Impact**    |

## Planning the Campaign

To begin working through this task:

1. **Download the required resources.**
2. **Launch the static site attached to this task.**

Your team has already decided to use the **Lockheed Martin Cyber Kill Chain** to emulate **APT 41**, as this adversary **best fits the client’s objectives and scope**.