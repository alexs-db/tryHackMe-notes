# Standards and Frameworks in Cyber Threat Intelligence

Standards and frameworks provide structures to rationalise the distribution and use of threat intelligence across industries. They also enable common terminology, which helps in collaboration and communication. Below are some essential standards and frameworks commonly used.

---

## MITRE ATT&CK

MITRE's ATT&CK framework supplies a common language for describing adversarial behaviour. Each technique—such as `T1059.001 PowerShell` or `T1048.003 DNS tunnel`—acts as a reference point between vendors, teammates, and auditors.

> **Example Use Case:**  
> As an L1 analyst, you can use the matrix during an investigation:
> 1. Match the behaviour in the alert to a tactic/technique pair.
> 2. Write the ID in your triage note:  
>    _"Observed T1071.001 (web-based C2) against FINANCE-TRYHATME-00"._
> 3. Pass the note to Level 2 or Incident Response; they instantly know which mitigations and threat-actor profiles apply.

*Image: MITRE ATT&CK Matrix*

---

## MITRE D3FEND

If ATT&CK catalogues how adversaries attack, D3FEND catalogues how defenders respond. Each entry maps to defensive tactics such as Credential Hardening or Data Obfuscation.

> **Example Use Case:**  
> - Your proxy raises a `T1048.003 DNS tunnel` alert.
> - Search D3FEND for the matching defensive technique: `D3—NTDN DNS—request analysis`.
> - The page lists practical controls: block extensive TXT records and alert on uncommon query entropy.
> - Add the most feasible control to your "next actions" field; you have just supplied a mitigation, not only a diagnosis.

---

## Cyber Kill Chain

The Cyber Kill Chain, developed by Lockheed Martin, breaks down adversary actions into steps. This helps analysts and defenders identify which stage-specific activities occurred during an attack.

*Image: Seven steps of the Cyber Kill Chain*

| Technique              | Purpose                                                                 | Examples                                             |
|------------------------|-------------------------------------------------------------------------|------------------------------------------------------|
| Reconnaissance         | Obtain information about the victim and attack tactics                   | Harvesting emails, OSINT, social media, network scans|
| Weaponisation          | Engineer malware based on attack needs and intentions                    | Exploit with backdoor, malicious Office document     |
| Delivery               | Deliver malware to the victim's system                                   | Email, weblinks, USB                                 |
| Exploitation           | Breach system vulnerabilities to execute code and establish persistence  | EternalBlue, Zero-Logon, etc.                        |
| Installation           | Install malware/tools to gain access                                     | Password dumping, backdoors, RATs                    |
| Command & Control      | Remotely control the compromised system, move laterally, escalate privs  | Empire, Cobalt Strike, etc.                          |
| Actions on Objectives  | Fulfil attack goals: financial gain, espionage, data exfiltration        | Data encryption, ransomware, public defacement       |

Over time, the kill chain has been expanded using other frameworks, such as ATT&CK, and a new Unified Kill Chain has been formulated.

---

## CVEs, CVSS, and the NVD

A SOC queue contains almost as many vulnerability notifications as malware alerts. As an SOC L1 analyst, you must understand how to identify and organise vulnerability notifications:

- **CVE (Common Vulnerabilities and Exposures):** Catalogue number for discovered vulnerabilities (e.g., `CVE-2023-4863`).
- **CVSS (Common Vulnerability Scoring System):** 0–10 severity scale with temporal and environmental modifiers.
- **NVD (National Vulnerability Database):** Repository linking CVE numbers to CVSS scores, exploits, and affected products.

---

## Sharing and Processing Intel

Threat intelligence can be retrieved from various platforms and feeds. When organisations publish fresh indicators, every peer that consumes and validates them strengthens collective defence and feeds back improvements. Two key standards are:

- **STIX:** Structured JSON schema for describing threat information.
- **TAXII:** Trusted Automated eXchange of Indicator Information; a set of secure APIs for exchanging threat intelligence in near real-time.

TAXII supports two sharing models:
- **Collection:** Threat intel is collected and hosted by a producer.
- **Channel:** Threat intel is published to users from a central server.

Sharing threat intelligence has benefits, such as near-real-time feeds that shorten the gap between another organisation's incident and one's preventive procedures. Community contributions also earn organisations reciprocal trust as valuable intelligence sources.

> **Note:**  
> Not every indicator should be shared. Privacy laws, customer NDAs, or internal competitive information may forbid disclosure. Sharing specific IOCs early can also tip off adversaries that their campaign has been detected.
