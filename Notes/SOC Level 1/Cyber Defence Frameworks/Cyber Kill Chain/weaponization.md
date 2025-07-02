## Weaponization Phase

After a successful reconnaissance stage, **"Megatron"** begins crafting a weapon of destruction. Preferring not to interact with the victim directly, he creates a "weaponizer"—a tool that, according to Lockheed Martin, combines malware and an exploit into a deliverable payload. Most attackers use automated tools or purchase malware from the Dark Web. More sophisticated actors, such as nation-sponsored APTs (Advanced Persistent Threat Groups), may write custom malware to ensure uniqueness and evade detection.

### Key Terminology

- **Malware:** Software designed to damage, disrupt, or gain unauthorized access to a computer.
- **Exploit:** Code that takes advantage of a vulnerability or flaw in an application or system.
- **Payload:** Malicious code executed on the target system.

Continuing with our adversary, "Megatron" chooses to buy a pre-written payload from the Dark Web, allowing him to focus on other attack phases.

### Common Weaponization Techniques

During the Weaponization phase, an attacker may:

- **Create an infected Microsoft Office document** containing malicious macros or VBA (Visual Basic for Applications) scripts.  
    *For more on macros and VBA, see [Intro to Macros and VBA For Script Kiddies](https://www.trustedsec.com/blog/intro-to-macros-and-vba-for-script-kiddies/) by TrustedSec.*
- **Develop a malicious payload or sophisticated worm,** implant it on USB drives, and distribute them publicly.
- **Select Command and Control (C2) techniques** for executing commands on the victim's machine or delivering additional payloads.  
    *Learn more about C2 techniques on [MITRE ATT&CK](https://attack.mitre.org/).*
- **Choose a backdoor implant** to access the target system, bypassing security mechanisms.