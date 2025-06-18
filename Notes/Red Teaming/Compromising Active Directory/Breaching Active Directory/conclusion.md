## Conclusion

A significant number of attack avenues can be followed to breach Active Directory (AD). We covered some of the most commonly used techniques during a red team exercise in this network. Due to the sheer size of the attack surface, new methods to recover that first set of AD credentials are constantly being discovered. Building a proper enumeration methodology and continuously updating it is essential to find that initial pair of credentials.

---

## Mitigations

Organisations can take several steps to mitigate these risks:

- **User awareness and training:** The weakest link in the cybersecurity chain is almost always users. Training users to be cautious about disclosing sensitive information and not trusting suspicious emails reduces this attack surface.
- **Limit the exposure of AD services and applications online:** Not all applications need to be accessible from the internet, especially those that support NTLM and LDAP authentication. These applications should be placed in an intranet accessible through a VPN, which can support multi-factor authentication for added security.
- **Enforce Network Access Control (NAC):** NAC can prevent attackers from connecting rogue devices to the network. However, it requires effort, as legitimate devices must be allowlisted.
- **Enforce SMB Signing:** Enforcing SMB signing prevents SMB relay attacks.
- **Follow the principle of least privilege:** Attackers may recover a set of AD credentials. By following the principle of least privilege, especially for service credentials, the risk associated with compromised credentials can be significantly reduced.

---

Now that we have breached AD, the next step is to enumerate AD to gain a better understanding of the domain structure and identify potential misconfigurations that can be exploited. This will be covered in the next room.

> **Note:** Remember to clear the DNS configuration!