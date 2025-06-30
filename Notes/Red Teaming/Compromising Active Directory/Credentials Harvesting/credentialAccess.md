# Credential Access

Credential access refers to techniques adversaries use to obtain credentials from compromised systems. Gaining access to user credentials allows attackers to reuse them or impersonate users, facilitating lateral movement and access to additional resources. Obtaining legitimate credentials is often preferred over exploiting vulnerabilities.

For more information, see the [MITRE ATT&CK framework (TA0006)](https://attack.mitre.org/tactics/TA0006/).

## Common Locations of Stored Credentials

Credentials may be stored insecurely in various locations, including:

- Clear-text files
- Database files
- Memory
- Password managers
- Enterprise vaults
- Active Directory
- Network sniffing

Let's discuss each in more detail.

---

## Clear-text Files

Attackers may search compromised machines for credentials stored in local or remote file systems. Clear-text files can include sensitive information such as passwords or private keys. According to MITRE ATT&CK, this is defined as [Unsecured Credentials: Credentials In Files (T1552.001)](https://attack.mitre.org/techniques/T1552/001/).

**Examples of clear-text files:**

- Command history
- Configuration files (e.g., web app, FTP)
- Files related to Windows applications (browsers, email clients)
- Backup files
- Shared files and folders
- Registry
- Source code

**Example:**  
PowerShell command history is saved at:  
`C:\Users\USER\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt`

**Searching for "password" in the Windows Registry:**
```shell
reg query HKLM /f password /t REG_SZ /s
reg query HKCU /f password /t REG_SZ /s
```

---

## Database Files

Applications may store settings, configurations, or credentials in local database files. These files are valuable targets for credential hunting. For example, credentials can be extracted from the local McAfee Endpoint database file. For more, see the THM room: Breaching AD.

---

## Password Managers

Password managers store and manage users' login information for websites and services. Examples include:

- Built-in Windows password manager
- Third-party: KeePass, 1Password, LastPass

Misconfigurations or vulnerabilities in these applications can expose stored data. Various tools can enumerate and extract sensitive data from password managers used by browsers and desktop applications.

This room covers accessing the Windows Credentials Manager and extracting passwords.

---

## Memory Dump

Operating system memory can contain sensitive information, such as:

- Clear-text credentials
- Cached passwords
- Active Directory tickets

Accessing memory typically requires administrator privileges. This room discusses extracting clear-text passwords and authentication tickets from memory.

---

## Active Directory

Active Directory (AD) stores information about users, groups, and computers. Enumerating AD is a key focus in red team assessments. While AD is robust, misconfigurations can make it vulnerable.

**Common AD misconfigurations that may leak credentials:**

- **User descriptions:** Passwords stored in user descriptions
- **Group Policy SYSVOL:** Leaked encryption keys can expose admin accounts (see Task 8)
- **NTDS:** Contains AD user credentials
- **AD attacks:** Various misconfigurations discussed in Task 9

---

## Network Sniffing

After gaining initial network access, attackers can perform network attacks, including Man-in-the-Middle (MitM) attacks against protocols in the AD environment. These attacks can steal authentication information, such as NTLM hashes, by spoofing trusted resources.
