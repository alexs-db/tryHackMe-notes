# Tools (Challenging)

Congratulations! You have reached the challenging stage for adversaries.

At this level, your detection capabilities against artifacts have improved significantly. Attackers are likely to give up trying to break into your network, or they may attempt to create a new tool to serve the same purpose. This often results in a "game over" for attackers, as they would need to invest resources into building a new tool, finding an alternative with similar potential, or acquiring new skills to use a different tool effectively.

Attackers may use various utilities to create malicious macro documents (maldocs) for spearphishing, backdoors for establishing Command and Control (C2) infrastructure, custom `.exe` and `.dll` files, payloads, or password crackers.

**Example:**  
A Trojan drops a suspicious file named `Stealer.exe` in the Temp folder.

**Execution of the suspicious binary:**

*(Insert screenshot or description here)*

Antivirus signatures, detection rules, and YARA rules are powerful tools at this stage to defend against attackers.

- **MalwareBazaar** and **Malshare** provide access to malware samples, malicious feeds, and YARA results, which are valuable for threat hunting and incident response.
- For detection rules, **SOC Prime Threat Detection Marketplace** is a great platform where security professionals share detection rules for various threats, including the latest CVEs exploited in the wild.

**Fuzzy hashing** is another strong defense against attacker tools. It allows similarity analysis—matching files with minor differences based on their fuzzy hash values. A common example is the use of **SSDeep**. The [SSDeep official website](https://ssdeep-project.github.io/ssdeep/) provides a comprehensive explanation of fuzzy hashing.

**Example of SSDeep from VirusTotal:**

*(Insert screenshot or description here)*

