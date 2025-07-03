# Initial Foothold Phases (Unified Kill Chain)

The main focus of these phases is for an attacker to gain access to a system or networked environment. Attackers employ various tactics to investigate systems for potential vulnerabilities and establish a foothold. Common tactics include reconnaissance to discover attack vectors such as applications and services.

Attackers may also create persistence mechanisms (e.g., files or processes) to maintain access. The Unified Kill Chain (UKC) recognizes that attackers often combine multiple tactics.

Below are the key phases of this section of the UKC:

---

## Reconnaissance ([MITRE Tactic TA0043](https://attack.mitre.org/tactics/TA0043/))

Techniques used to gather information about the target, through passive or active means. Information collected is used in later stages.

**Examples of information gathered:**
- Systems and services running on the target (useful for weaponization and exploitation)
- Contact lists or employee names (for social engineering or phishing)
- Potential credentials (for pivoting or initial access)
- Network topology and connected systems

---

## Weaponization ([MITRE Tactic TA0001](https://attack.mitre.org/tactics/TA0001/))

Setting up infrastructure to perform the attack, such as:
- Command and control servers
- Systems for catching reverse shells or delivering payloads

---

## Social Engineering ([MITRE Tactic TA0001](https://attack.mitre.org/tactics/TA0001/))

Manipulating employees to aid the attack.

**Examples:**
- Getting a user to open a malicious attachment
- Impersonating a web page to steal credentials
- Impersonating staff (e.g., requesting password resets or physical access)

---

## Exploitation ([MITRE Tactic TA0002](https://attack.mitre.org/tactics/TA0002/))

Abusing vulnerabilities to execute code.

**Examples:**
- Uploading and executing a reverse shell
- Interfering with scripts to execute code
- Exploiting web application vulnerabilities

---

## Persistence ([MITRE Tactic TA0003](https://attack.mitre.org/tactics/TA0003/))

Maintaining access to a compromised system.

**Examples:**
- Creating a persistent service
- Adding the system to a command & control server
- Leaving backdoors (e.g., reverse shell triggered by admin login)

---

## Defence Evasion ([MITRE Tactic TA0005](https://attack.mitre.org/tactics/TA0005/))

Evading defensive measures.

**Examples:**
- Bypassing web application firewalls
- Evading network firewalls
- Avoiding anti-virus detection
- Circumventing intrusion detection systems

This phase is valuable for defenders to analyze attacks and improve security.

---

## Command & Control ([MITRE Tactic TA0011](https://attack.mitre.org/tactics/TA0011/))

Establishing communication between the attacker and the compromised system.

**Capabilities:**
- Executing commands
- Stealing data and credentials
- Pivoting to other systems

---

## Pivoting ([MITRE Tactic TA0008](https://attack.mitre.org/tactics/TA0008/))

Reaching other systems within a network that are not directly accessible.

**Example:**
- Using access to a public web server to attack internal systems not exposed to the internet

---