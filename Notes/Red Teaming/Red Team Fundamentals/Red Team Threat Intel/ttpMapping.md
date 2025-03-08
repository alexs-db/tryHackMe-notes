# TTP Mapping and Kill Chain

## Introduction

TTP Mapping is used by the red cell to associate an adversary's **TTPs (Tactics, Techniques, and Procedures)** with a **standard cyber kill chain**. This approach allows a red team to plan an engagement by imitating a specific adversary.

### Selecting the Adversary

An adversary can be chosen based on:

- **Target industry**
- **Employed attack vectors**
- **Country of origin**
- **Other factors**

In this example, we have selected **APT39**, an Iranian cyber-espionage group targeting various sectors.

## Kill Chain Framework Used

We will use the **Lockheed Martin Cyber Kill Chain** to map TTPs.

## Collecting TTPs

### MITRE ATT&CK

**MITRE ATT&CK** provides **IDs** and descriptions of categorized TTPs. The **ATT&CK Navigator** tool helps visualize and organize these TTPs within the kill chain.

#### Using ATT&CK Navigator:

1. Go to the targeted group's summary page.
2. Next to **"Techniques Used"**, click on **"ATT&CK Navigator Layers"**.
3. Select **"View"** to open the highlighted TTPs in a new tab.

## APT39 Kill Chain with Mapped TTPs

| Kill Chain Phase        | Identified TTPs |
|-------------------------|----------------|
| **Reconnaissance**      | No identified TTPs, using internal methodologies |
| **Weaponization**       | - Command and Scripting Interpreter<br>- PowerShell<br>- Python<br>- VBA<br>- User executed malicious attachments |
| **Delivery**           | - Exploit Public-Facing Applications<br>- Spearphishing |
| **Exploitation**       | - Registry modification<br>- Scheduled tasks<br>- Keylogging<br>- Credential dumping |
| **Installation**       | - Ingress tool transfer<br>- Proxy usage |
| **Command & Control**  | - Web protocols (HTTP/HTTPS)<br>- DNS |
| **Actions on Objectives** | - Exfiltration over C2 |

## Additional Threat Intelligence Tools and Frameworks

Although **MITRE ATT&CK** is a strong foundation, other platforms can complement the analysis, such as:

- **OST Map**: A visual map linking multiple threat actors and their TTPs.
- **Mandiant Advantage**
- **Ontic**
- **CrowdStrike Falcon**

These platforms assist red teamers in refining **adversary emulation** and **TTP Mapping**.