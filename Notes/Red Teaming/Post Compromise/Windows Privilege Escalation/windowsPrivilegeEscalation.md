# Windows Privilege Escalation

Privilege escalation involves leveraging access to a host with "user A" to gain access to "user B" by exploiting weaknesses in the target system. While the goal is often to escalate to an administrative account, there may be cases where escalating to another unprivileged account is necessary before obtaining administrative privileges.

Accessing different accounts can sometimes be as simple as finding credentials in unsecured text files or spreadsheets. However, in other cases, it may require exploiting one or more of the following weaknesses:

- Misconfigurations in Windows services or scheduled tasks
- Excessive privileges assigned to an account
- Vulnerable software
- Missing Windows security patches

Before diving into specific techniques, let's review the different account types on a Windows system.

## Windows Users

Windows systems primarily have two types of users. Based on their access levels, users can be categorized into the following groups:

- **Administrators**: These users have the highest privileges. They can modify any system configuration and access all files on the system.
- **Standard Users**: These users have limited access. They can perform basic tasks but cannot make significant or permanent changes to the system. Their access is typically restricted to their own files.

Users with administrative privileges belong to the **Administrators** group, while standard users are part of the **Users** group.

### Built-in Accounts

In addition to regular user accounts, Windows includes special built-in accounts used by the operating system. These accounts are relevant in the context of privilege escalation:

- **SYSTEM / LocalSystem**: This account is used by the operating system for internal tasks. It has full access to all files and resources on the host, with privileges exceeding those of administrators.
- **Local Service**: A default account used to run Windows services with minimal privileges. It uses anonymous connections over the network.
- **Network Service**: A default account used to run Windows services with minimal privileges. It uses the computer's credentials to authenticate over the network.

These built-in accounts are created and managed by Windows. While they cannot be used like regular accounts, it is sometimes possible to gain their privileges by exploiting specific services.