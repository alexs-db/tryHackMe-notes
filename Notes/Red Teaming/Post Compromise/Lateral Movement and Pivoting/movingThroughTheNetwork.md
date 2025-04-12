# Lateral Movement and Pivoting

## What is Lateral Movement?

Lateral movement refers to the techniques attackers use to navigate through a network after gaining initial access. This is essential for several reasons:

- Achieving attackers' objectives.
- Bypassing network restrictions.
- Establishing additional entry points.
- Creating confusion and avoiding detection.

While often depicted as a step in a linear cyber kill chain, lateral movement is part of a cycle. Attackers use available credentials to move laterally, gaining access to new machines, elevating privileges, and extracting credentials. This process repeats until the attackers reach their final goal.

### Example Scenario

Imagine a red team engagement where the goal is to access an internal code repository. The initial compromise occurs via a phishing campaign targeting a Marketing department workstation. Marketing workstations typically have restricted access to critical network services, such as administrative protocols or code repositories.

To reach the target, attackers might:

1. Elevate privileges on the Marketing workstation.
2. Extract local user password hashes.
3. Use a local administrator's credentials to access another machine, such as `DEV-001-PC`, owned by a developer.
4. From the developer's machine, access the code repository.

### Simple Lateral Movement

Lateral movement can also help evade detection. For example, even if the Marketing workstation had direct access to the code repository, connecting through the developer's PC would appear less suspicious in audit logs.

---

## The Attacker's Perspective

Attackers use various methods for lateral movement. The simplest involves standard administrative protocols like:

- WinRM
- RDP
- VNC
- SSH

This approach can mimic regular user behavior if planned carefully. For instance, an IT user connecting to a web server via RDP might seem normal, but a local admin connecting from a Marketing-PC to `DEV-001-PC` could raise suspicion.

Modern attackers also employ stealthier techniques to make detection more challenging. While no method is foolproof, the goal is to remain as silent as possible.

---

## Administrators and UAC

Lateral movement often relies on administrator credentials. However, there are two types of administrators to consider:

1. **Local accounts** in the local Administrators group.
2. **Domain accounts** in the local Administrators group.

### User Account Control (UAC) Restrictions

By default, local administrators (except the default Administrator account) face restrictions due to UAC:

- They cannot remotely perform administrative tasks via RPC, SMB, or WinRM.
- They are logged in with a filtered medium integrity token, limiting privileged actions.
- Full privileges are only granted to the default Administrator account.

Domain accounts with local administrative privileges are not subject to these restrictions and have full administrative privileges.

### Disabling UAC

This security feature can be disabled, and in some cases, there may be no difference between local and domain accounts in the Administrators group. However, if a lateral movement technique fails, it might be due to UAC restrictions on non-default local administrators. 

For more details, refer to [Microsoft's documentation on UAC](https://learn.microsoft.com/).