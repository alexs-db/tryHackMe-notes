## Phase: Action on Objectives

This phase concludes the adversary’s attack journey, where they have gained access to critical assets and can fulfill their attack goals. These goals typically target the **confidentiality, integrity, and availability (CIA) triad**.

---

### Attacker Tactics

#### 1. Collection ([MITRE Tactic TA0009](https://attack.mitre.org/tactics/TA0009/))

After gaining access, adversaries gather valuable data of interest, compromising data confidentiality. Common target sources include:

- Drives
- Browsers
- Audio and video files
- Email

This stage often leads to the next phase: **Exfiltration**.

#### 2. Exfiltration ([MITRE Tactic TA0010](https://attack.mitre.org/tactics/TA0010/))

Adversaries steal data, often packaging it with encryption and compression to evade detection. Previously established C2 channels and tunnels are used to transfer the data out of the environment.

#### 3. Impact ([MITRE Tactic TA0040](https://attack.mitre.org/tactics/TA0040/))

To compromise data integrity and availability, adversaries may:

- Manipulate, interrupt, or destroy assets
- Remove account access
- Wipe disks
- Encrypt data (e.g., ransomware)
- Deface systems
- Launch denial of service (DoS) attacks

The goal is to disrupt business and operational processes.

---

### Objectives

With full access to systems and networks, adversaries pursue their strategic goals. Examples include:

- **Financial Motivation:** Encrypting files/systems with ransomware and demanding payment for decryption.
- **Reputation Damage:** Releasing private and confidential information to the public.
