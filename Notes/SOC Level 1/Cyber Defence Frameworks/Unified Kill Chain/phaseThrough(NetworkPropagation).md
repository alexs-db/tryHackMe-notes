## Network Propagation Phase

After establishing a foothold on the target network, attackers aim to expand their access and privileges to achieve their objectives. They set up a base on a compromised system, using it as a pivot point to gather information about the internal network.

---

### Pivoting ([MITRE Tactic TA0008](https://attack.mitre.org/tactics/TA0008))

Once access is gained, the attacker uses the compromised system as a staging site and a tunnel between their command operations and the victim’s network. This system also serves as a distribution point for malware and backdoors in later stages.

---

### Discovery ([MITRE Tactic TA0007](https://attack.mitre.org/tactics/TA0007))

The adversary collects information about the system and its network, building a knowledge base that includes:

- Active user accounts
- Granted permissions
- Applications and software in use
- Web browser activity
- Files, directories, and network shares
- System configurations

---

### Privilege Escalation ([MITRE Tactic TA0004](https://attack.mitre.org/tactics/TA0004))

After gathering information, the attacker attempts to gain higher privileges on the pivot system by exploiting vulnerabilities and misconfigurations. Possible elevated access levels include:

- SYSTEM/ROOT
- Local Administrator
- User account with admin-like access
- User account with specific access or functions

---

### Execution ([MITRE Tactic TA0002](https://attack.mitre.org/tactics/TA0002))

The attacker deploys malicious code from the pivot system, such as:

- Remote trojans
- C2 scripts
- Malicious links
- Scheduled tasks

These actions help maintain persistence on the system.

---

### Credential Access ([MITRE Tactic TA0006](https://attack.mitre.org/tactics/TA0006))

In conjunction with privilege escalation, the attacker attempts to steal account names and passwords using methods like keylogging and credential dumping. Using legitimate credentials helps evade detection.

---

### Lateral Movement ([MITRE Tactic TA0008](https://attack.mitre.org/tactics/TA0008))

With stolen credentials and elevated privileges, the attacker moves through the network to access additional systems and achieve their objectives. Stealthy techniques are preferred to avoid detection.
