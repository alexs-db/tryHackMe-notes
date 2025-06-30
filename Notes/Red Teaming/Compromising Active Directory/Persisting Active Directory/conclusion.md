# Conclusion

There are several ways to persist in Active Directory (AD). Some techniques are more resilient than others. To ensure your persistence cannot be easily removed by defenders, you must think creatively. Persistence should not wait until full domain compromise—after each round of lateral movement and privilege escalation, persistence should be deployed.

---

## Additional Persistence Techniques

In this network, we covered several AD persistence techniques. This is not an exhaustive list. Here are some additional techniques worth mentioning:

- **Skeleton Keys:** Using Mimikatz, a skeleton key can be deployed, creating a default password that works for any domain account. Normal passwords still function, making detection difficult. The default password can be used to impersonate any account.
- **Directory Service Restore Mode (DSRM):** Domain controllers have a built-in DSRM administrator account, set during server promotion and rarely changed. Attackers can extract this password with Mimikatz to gain persistent administrative access to domain controllers.
- **Malicious Security Support Provider (SSP):** By adding a new SSP (e.g., Mimikatz's mimilib), credentials from authentication attempts can be logged to a file or sent to a remote location, providing ongoing access.
- **Computer Accounts:** Machine account passwords are typically rotated every 30 days. By altering a machine account's password, automatic rotation can be stopped. Granting the machine account administrative access to other machines allows it to be used like a normal account, with minimal signs of persistence.

Note: This module focused on AD-specific persistence. Local persistence techniques on domain-joined hosts can also provide AD persistence.

---

## Mitigations

AD persistence can be difficult to defend against. In some cases, a complete domain rebuild may be required. However, several detection and mitigation strategies exist:

- **Monitor Anomalous Account Logons:** Unusual logon events, especially those breaking the tiering model, can indicate persistence.
- **Write Specific Detection Rules:** Monitor for events such as machine account password changes, permissive ACL updates, or new GPO creation.
- **Protect Privileged Resources:** The best defense is to secure privileged accounts and resources. While low-privilege access can deploy persistence, the most dangerous techniques require privileged access.

---

This concludes the AD module. You have learned the basics of AD, how to breach and enumerate an AD environment, perform exploitation, and establish persistence. This is just an introduction—there is much more to learn about AD security. Continue exploring and expanding your knowledge!