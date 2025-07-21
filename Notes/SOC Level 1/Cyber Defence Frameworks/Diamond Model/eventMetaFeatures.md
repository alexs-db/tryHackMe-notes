# Diamond Model Meta-Features

Six possible meta-features can be added to the Diamond Model. While not required, these meta-features provide valuable context and intelligence.

---

## 1. Timestamp

- **Definition:** The date and time of the event.
- **Details:** Each event can be recorded with a start and stop time (e.g., `2021-09-12 02:10:12.136`).
- **Importance:** Timestamps help identify patterns and group malicious activities. For example, an attack at 3 am in the US may suggest a different time zone for the attacker.

---

## 2. Phase

- **Definition:** The stages of an intrusion, attack, or breach.
- **Details:** Malicious activities typically occur as a sequence of events, not as single incidents.
- **Example Phases (Cyber Kill Chain):**
    1. Reconnaissance
    2. Weaponization
    3. Delivery
    4. Exploitation
    5. Installation
    6. Command & Control
    7. Actions on Objective

*Example:* An attacker researches the target, exploits vulnerabilities, establishes command and control, and exfiltrates data.

---

## 3. Result

- **Definition:** The outcome and post-conditions of an adversary’s operations.
- **Details:** Results may be labeled as `success`, `failure`, or `unknown`. They can also relate to the CIA triad:
    - Confidentiality Compromised
    - Integrity Compromised
    - Availability Compromised
- **Note:** Documenting post-conditions (e.g., data exfiltration, information gathered) is important, even if not always known.

---

## 4. Direction

- **Definition:** Describes the flow of the intrusion event.
- **Possible Values:**
    - Victim-to-Infrastructure
    - Infrastructure-to-Victim
    - Infrastructure-to-Infrastructure
    - Adversary-to-Infrastructure
    - Infrastructure-to-Adversary
    - Bidirectional
    - Unknown

---

## 5. Methodology

- **Definition:** General classification of the intrusion.
- **Examples:** Phishing, DDoS, breach, port scan, etc.

---

## 6. Resources

- **Definition:** External resources required for the intrusion event.
- **Examples:**
    - **Software:** Operating systems, virtualization tools, Metasploit
    - **Knowledge:** Skills to use tools or execute attacks
    - **Information:** Credentials, sensitive data
    - **Hardware:** Servers, routers, workstations
    - **Funds:** Money for domains or infrastructure
    - **Facilities:** Electricity, shelter
    - **Access:** Network paths, ISP access

---