# The Lay of the Land: Applications and Services

This task will expand our knowledge needed to learn more about the system. We discussed account discovery and security products within the system in previous tasks. We will continue learning more about the system, including:

- Installed applications
- Services and processes
- Sharing files and printers
- Internal services: DNS and local web applications

It is necessary to understand what the system provides in order to get the benefit of the information.

---

## Installed Applications

First, we start enumerating the system for installed applications by checking the application's name and version. As a red teamer, this information will benefit us. We may find vulnerable software installed to exploit and escalate our system privileges. Also, we may find some information, such as plain-text credentials, left on the system that belongs to other systems or services.

We will be using the `wmic` Windows command to list all installed applications and their versions:

```powershell
PS C:\Users\thm> wmic product get name,version
Name                                                            Version
Microsoft Visual C++ 2019 X64 Minimum Runtime - 14.28.29910     14.28.29910
AWS Tools for Windows                                           3.15.1248
Amazon SSM Agent                                                3.0.529.0
aws-cfn-bootstrap                                               2.0.5
AWS PV Drivers                                                  8.3.4
Microsoft Visual C++ 2019 X64 Additional Runtime - 14.28.29910  14.28.29910
```

Another interesting thing is to look for particular text strings, hidden directories, and backup files. We can use the PowerShell cmdlet `Get-ChildItem` as follows:

```powershell
PS C:\Users\thm> Get-ChildItem -Hidden -Path C:\Users\kkidd\Desktop\
```

---

## Services and Processes

Windows services enable the system administrator to create long-running executable applications in their own Windows sessions. Sometimes, Windows services have misconfigured permissions, which escalate the current user's access level. Therefore, we must look at running services and perform services and processes reconnaissance. For more details, you can read about process discovery on [MITRE ATT&CK](https://attack.mitre.org/).

Process discovery is an enumeration step to understand what the system provides. The red team should gather information and details about running services and processes on a system. This information could help us understand common software running on other systems in the network. For example, the compromised system may have a custom client application used for internal purposes. Custom internally developed software is the most common root cause of escalation vectors. Thus, it is worth digging deeper to get details about the current processes.

For more details about core Windows processes from the blue team perspective, check out the TryHackMe room: [Core Windows Process](https://tryhackme.com/room/corewindowsprocess).

---

## Sharing Files and Printers

Sharing files and network resources is commonly used in personal and enterprise environments. System administrators may misconfigure access permissions, which could provide useful information about other accounts and systems. For more information on printer hacking, we suggest trying out the following TryHackMe room: [Printer Hacking 101](https://tryhackme.com/room/printerhacking101).

---

## Internal Services: DNS, Local Web Applications, etc.

Internal network services are another source of information to expand our knowledge about other systems and the entire environment. To get more details about network services used for external and internal purposes, we suggest trying out the following TryHackMe rooms: [Network Services](https://tryhackme.com/room/networkservices) and [Network Services 2](https://tryhackme.com/room/networkservices2).

The following are some of the internal services that are commonly used and of interest:

- **DNS Services**
- **Email Services**
- **Network File Share**
- **Web Applications**
- **Database Services**