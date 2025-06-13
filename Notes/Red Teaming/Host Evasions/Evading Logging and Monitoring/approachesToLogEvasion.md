## Approaches to Log Evasion

Before diving into modern and technical evasion techniques, let’s review the various approaches available and their impacts on both attackers and defenders.

When considering log evasion, one might initially think that simply destroying or tampering with logs is a viable option. However, following security best practices, most modern environments employ **log forwarding**. This means the Security Operations Center (SOC) forwards logs from the host machine to a central server or indexer. Even if an attacker deletes logs from the host, they may have already been forwarded and secured elsewhere.

If an attacker manages to destroy all logs before they are forwarded—or if forwarding is not in place—this can itself raise an alert. The absence of logs from a device can arouse suspicion and prompt investigation. Even if an attacker controls which logs are removed or forwarded, defenders can still detect tampering.

### Key Windows Event IDs for Log Tampering

| Event ID | Purpose                                              |
|----------|------------------------------------------------------|
| 1102     | Windows Security audit log was cleared               |
| 104      | Log file was cleared                                 |
| 1100     | Windows Event Log service was shut down              |

These event IDs help monitor attempts to destroy logs, a process sometimes called “log smashing.” This presents a clear risk to attackers, as such actions are likely to be detected. While it is possible to further bypass these mitigations or tamper with logs, attackers must carefully assess the operational security (OPSEC) risks, especially when unaware of the environment’s security practices.

If direct log destruction is too aggressive, a more strategic approach is needed. Attackers should focus on understanding which logs a malicious technique may generate to maintain the environment's integrity. By knowing what may be monitored, they can utilize or adapt published evasion methods.

Most published techniques target **Event Tracing for Windows (ETW)** components, as this provides the most control over the tracing process.

---

This room will break down some of the most common published techniques, as well as a more modern technique that allows for a wide range of control.
