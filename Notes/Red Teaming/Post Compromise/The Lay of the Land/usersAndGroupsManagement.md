# Users and Groups Management in Active Directory

In this task, we will learn more about users and groups, especially within the Active Directory. Gathering information about the compromised machine is essential for the next stage. Account discovery is the first step once we have gained initial access to the compromised machine to understand what we have and what other accounts are in the system.

## Active Directory Accounts Overview

An Active Directory environment contains various accounts with the necessary permissions, access, and roles for different purposes. Common Active Directory service accounts include:

- **Built-in Local User Accounts**: Used to manage the system locally, not part of the AD environment.
- **Domain User Accounts**: Have access to the Active Directory environment and can use AD services (managed by AD).
- **Managed Service Accounts**: Limited domain user accounts with higher privileges to manage AD services.
- **Domain Administrators**: User accounts with permissions to manage information in an Active Directory environment, including configurations, users, groups, permissions, roles, and services.

One of the red team goals during engagement is to hunt for information that leads to a domain administrator, as they have complete control over the AD environment.

### Common Active Directory Administrator Accounts

| Account Type            | Description                                           |
|-------------------------|-------------------------------------------------------|
| **BUILTIN\Administrator** | Local admin access on a domain controller            |
| **Domain Admins**        | Administrative access to all resources in the domain |
| **Enterprise Admins**    | Available only in the forest root                    |
| **Schema Admins**        | Capable of modifying domain/forest; useful for red teamers |
| **Server Operators**     | Can manage domain servers                            |
| **Account Operators**    | Can manage users that are not in privileged groups   |

## Enumerating the Windows Machine

Now that we understand the various account types within the AD environment, let's enumerate the Windows machine we have access to during the initial access stage. As a current user, we have specific permissions to view or manage things within the machine and the AD environment.

### Active Directory Enumeration

Enumerating the AD environment requires different tools and techniques. Once we confirm that the machine is part of the AD environment, we can start hunting for any valuable information that may be used later. In this stage, we will use PowerShell to enumerate users and groups.

#### Enumerating All Active Directory User Accounts

The following PowerShell command retrieves all Active Directory user accounts. Note the use of the `-Filter` argument:

```powershell
PS C:\Users\thm> Get-ADUser -Filter *
```

Example output:

```
DistinguishedName : CN=Administrator,CN=Users,DC=thmredteam,DC=com
Enabled           : True
GivenName         :
Name              : Administrator
ObjectClass       : user
ObjectGUID        : 4094d220-fb71-4de1-b5b2-ba18f6583c65
SamAccountName    : Administrator
SID               : S-1-5-21-1966530601-3185510712-10604624-500
Surname           :
UserPrincipalName :
```

#### Using LDAP Hierarchical Tree Structure

We can also use the LDAP hierarchical tree structure to find a user within the AD environment. The Distinguished Name (DN) is a collection of comma-separated key-value pairs used to identify unique records within the directory. For example:

```
CN=User1,CN=Users,DC=thmredteam,DC=com
```

This can be visualized as follows:

- **CN**: Common Name
- **OU**: Organizational Unit Name
- **DC**: Domain Component

#### Filtering by SearchBase

Using the `-SearchBase` option, we can specify a specific Common Name (CN) in the Active Directory. For example, to list any user(s) that are part of the `Users` CN:

```powershell
PS C:\Users\thm> Get-ADUser -Filter * -SearchBase "CN=Users,DC=THMREDTEAM,DC=COM"
```

Example output:

```
DistinguishedName : CN=Administrator,CN=Users,DC=thmredteam,DC=com
Enabled           : True
GivenName         :
Name              : Administrator
ObjectClass       : user
ObjectGUID        : 4094d220-fb71-4de1-b5b2-ba18f6583c65
SamAccountName    : Administrator
SID               : S-1-5-21-1966530601-3185510712-10604624-500
Surname           :
UserPrincipalName :
```

Note that the result may contain more than one user depending on the configuration of the CN. Try the command to find all users within the `THM` Organizational Unit (OU) and answer the related questions.