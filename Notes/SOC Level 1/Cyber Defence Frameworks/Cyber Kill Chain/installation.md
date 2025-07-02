## Persistence in the Cyber Kill Chain

As learned in the Weaponization phase, a backdoor allows an attacker to bypass security measures and maintain hidden access to a system. A backdoor is also known as an access point.

Once an attacker gains access, they often want to ensure they can regain entry if their connection is lost, their initial access is removed, or the system is patched. In such cases, installing a **persistent backdoor** is crucial. This enables the attacker to access the compromised system in the future. For more details on persistence techniques in Windows, refer to the [Windows Persistence Room on TryHackMe](https://tryhackme.com/room/windows-persistence).

### Common Persistence Techniques

- **Installing a Web Shell:**  
    Attackers may upload a malicious script (web shell) written in languages like ASP, PHP, or JSP to a web server. Web shells are simple, often disguised by their file extensions (.php, .asp, .aspx, .jsp, etc.), making them hard to detect. [Read more about web shell attacks in this Microsoft article.](https://www.microsoft.com/security/blog/2020/02/28/web-shell-attacks-continue-to-rise/)

- **Installing a Backdoor on the Victim's Machine:**  
    Tools like Meterpreter (a Metasploit Framework payload) provide attackers with an interactive shell to remotely control the victim's machine and execute malicious code.

- **Creating or Modifying Windows Services:**  
    Known as [MITRE ATT&CK technique T1543.003](https://attack.mitre.org/techniques/T1543/003/), attackers can create or alter Windows services to run malicious scripts or payloads regularly. Tools such as `sc.exe` (for managing Windows services) and `reg` (for editing the registry) are commonly used. Attackers may also disguise malicious services with legitimate-sounding names.

- **Adding Entries to Run Keys or Startup Folder:**  
    By adding the malicious payload to the Windows Registry "run keys" or the Startup Folder, the payload executes each time a user logs in. There are both user-specific and system-wide startup folders. [Learn more about Registry Run Keys / Startup Folder persistence (MITRE ATT&CK).](https://attack.mitre.org/techniques/T1547/001/)

### Additional Techniques

- **Timestomping:**  
    To evade forensic detection, attackers may use timestomping to modify a file's timestamps (modify, access, create, and change times), making malware appear as part of a legitimate program.

---

By understanding these persistence techniques, defenders can better detect and mitigate ongoing threats within their environments.