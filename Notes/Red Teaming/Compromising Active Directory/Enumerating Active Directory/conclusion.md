# Conclusion

Enumerating Active Directory (AD) is a significant task. Proper enumeration helps you understand the domain's structure and identify potential attack paths for privilege escalation or lateral movement.

---

## Additional Enumeration Techniques

In this network, we covered several AD enumeration techniques. This is not an exhaustive list. Here are some additional techniques worth mentioning:

- **LDAP Enumeration:** Any valid AD credentials can bind to a Domain Controller's LDAP interface, allowing LDAP search queries to enumerate AD objects.
- **PowerView:** Part of the PowerSploit project, PowerView is a recon script useful for semi-manual AD object enumeration, even though the project is no longer maintained.
- **Windows Management Instrumentation (WMI):** WMI can enumerate information from Windows hosts. The `root\directory\ldap` provider allows interaction with AD, and PowerShell can leverage this for AD enumeration.

> **Note:**  
> This room focused on enumerating the overall AD domain structure, not just identifying misconfigurations or weaknesses. Enumeration targeting weaknesses (e.g., insecure shares, tiering model breaks) will be discussed in future rooms.

---

## Mitigations

Defending against AD enumeration is challenging, as many techniques mimic normal network behavior. However, some detection strategies include:

- **Monitor LogOn Events:** Tools like SharpHound generate many LogOn events from a single account. Detection rules can flag this behavior.
- **Signature Detection:** Create rules for known enumeration tools (e.g., SharpHound binaries, AD-RSAT tooling).
- **Monitor Command Usage:** Unless required by employees, monitor Command Prompt and PowerShell usage to detect unauthorized enumeration attempts.

> **Tip:**  
> Blue teams should regularly use these enumeration techniques to find and fix misconfigurations. If resolved, attackers will have fewer opportunities for privilege escalation or lateral movement.

---

Now that AD enumeration is complete, the next step is privilege escalation and lateral movement within the domain. This will be covered in the next room.