# Windows Enumeration

In this task, we assume you have access to `cmd` on a Microsoft Windows host. You might have gained this access by exploiting a vulnerability and getting a shell or a reverse shell. You may also have installed a backdoor or set up an SSH server on a system you exploited. In all cases, the commands below require `cmd` to run.

This task focuses on enumerating an MS Windows host. For enumerating MS Active Directory, check the **Enumerating Active Directory** room. For privilege escalation on an MS Windows host, refer to the **Windows Privesc 2.0** room.

We recommend starting the **AttackBox** and **Machine** to experiment and answer the questions at the end of this task.

---

## System Information

### `systeminfo`
Provides detailed information about the system, such as its build number and installed patches.

```cmd
C:\>systeminfo
```

Example output:
```
Host Name:                 WIN-SERVER-CLI
OS Name:                   Microsoft Windows Server 2022 Standard
OS Version:                10.0.20348 N/A Build 20348
OS Manufacturer:           Microsoft Corporation
[...]
Hotfix(s):                 3 Hotfix(s) Installed.
                           [01]: KB5013630
                           [02]: KB5013944
                           [03]: KB5012673
Network Card(s):           1 NIC(s) Installed.
                           [01]: Intel(R) 82574L Gigabit Network Connection
[...]
```

### `wmic qfe get Caption,Description`
Lists installed updates.

```cmd
C:\>wmic qfe get Caption,Description
```

Example output:
```
Caption                                     Description      
http://support.microsoft.com/?kbid=5013630  Update
https://support.microsoft.com/help/5013944  Security Update
                                            Update
```

### `net start`
Lists started Windows services.

```cmd
C:\>net start
```

Example output (partial):
```
These Windows services are started:
   Base Filtering Engine
   Certificate Propagation
   Client License Service (ClipSVC)
   [...]
   Windows Time
   Windows Update
   WinHTTP Web Proxy Auto-Discovery Service
   Workstation
The command completed successfully.
```

### `wmic product get name,version,vendor`
Lists installed applications.

```cmd
C:\>wmic product get name,version,vendor
```

Example output (partial):
```
Name                                                            Vendor                                   Version
Microsoft Visual C++ 2019 X64 Minimum Runtime - 14.28.29910     Microsoft Corporation                    14.28.29910
[...]
```

---

## User Information

### `whoami` and `whoami /priv`
Displays the current user and their privileges.

```cmd
C:\>whoami
C:\>whoami /priv
```

Example output:
```
win-server-cli\strategos

PRIVILEGES INFORMATION
----------------------
Privilege Name                            Description                                                        State
========================================= ================================================================== =======
SeIncreaseQuotaPrivilege                  Adjust memory quotas for a process                                 Enabled
SeSecurityPrivilege                       Manage auditing and security log                                   Enabled
[...]
```

### `whoami /groups`
Lists the groups the user belongs to.

```cmd
C:\>whoami /groups
```

Example output (partial):
```
GROUP INFORMATION
-----------------
Group Name                                                    Type             SID          Attributes
============================================================= ================ ============ ===============================================================
Everyone                                                      Well-known group S-1-1-0      Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Local account and member of Administrators group Well-known group S-1-5-114    Mandatory group, Enabled by default, Enabled group
[...]
```

### `net user`
Lists user accounts.

```cmd
C:\>net user
```

Example output:
```
User accounts for \\WIN-SERVER-CLI
-------------------------------------------------------------------------------
Administrator            DefaultAccount           Guest
michael                  peter                    strategos
WDAGUtilityAccount
The command completed successfully.
```

### `net localgroup`
Lists available groups.

```cmd
C:\>net localgroup
```

Example output (partial):
```
Aliases for \\WIN-SERVER-CLI
-------------------------------------------------------------------------------
*Access Control Assistance Operators
*Administrators
*Backup Operators
[...]
```

### `net localgroup administrators`
Lists users in the local administrators group.

```cmd
C:\>net localgroup administrators
```

Example output:
```
Alias name     administrators
Comment        Administrators have complete and unrestricted access to the computer/domain

Members
-------------------------------------------------------------------------------
Administrator
michael
peter
strategos
The command completed successfully.
```

### `net accounts`
Displays local account settings.

```cmd
C:\>net accounts
C:\>net accounts /domain
```

---

## Networking Information

### `ipconfig`
Displays network configuration.

```cmd
C:\>ipconfig
```

Example output:
```
Windows IP Configuration

Ethernet adapter Ethernet0:
   Connection-specific DNS Suffix  . : localdomain
   Link-local IPv6 Address . . . . . : fe80::3dc5:78ef:1274:a740%5
   IPv4 Address. . . . . . . . . . . : 10.20.30.130
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Default Gateway . . . . . . . . . : 10.20.30.2
```

### `netstat`
Displays active connections and listening ports.

```cmd
C:\>netstat -abno
```

Example output (partial):
```
Active Connections
  Proto  Local Address          Foreign Address        State           PID
  TCP    0.0.0.0:22             0.0.0.0:0              LISTENING       2016
 [sshd.exe]
  TCP    0.0.0.0:135            0.0.0.0:0              LISTENING       924
  RpcSs
 [svchost.exe]
  TCP    10.20.30.130:22        10.20.30.1:39956       ESTABLISHED     2016
 [sshd.exe]
[...]
```

### `arp -a`
Displays ARP entries to discover systems on the same LAN.

```cmd
C:\>arp -a
```

Example output:
```
Interface: 10.10.204.175 --- 0x4 
  Internet Address      Physical Address      Type
  10.10.0.1             02-c8-85-b5-5a-aa     dynamic
  10.10.16.117          02-f2-42-76-fc-ef     dynamic
  [...]
```