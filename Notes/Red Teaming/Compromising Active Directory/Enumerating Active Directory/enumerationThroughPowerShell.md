# Enumerating Active Directory with PowerShell

## Introduction

PowerShell is an upgrade to Command Prompt, first released by Microsoft in 2006. It provides all the standard functionality of Command Prompt, plus access to cmdlets (pronounced *command-lets*), which are .NET classes designed for specific functions. While you can write your own cmdlets (as the creators of PowerView did), the built-in ones are already very powerful.

After installing the AD-RSAT tooling (see Task 3), the associated cmdlets are automatically available—over 50 in total. Refer to the official documentation for a complete list.

To upgrade your SSH terminal to a PowerShell terminal, use:

```sh
powershell
```

---

## Enumerating Users

Use the `Get-ADUser` cmdlet to enumerate Active Directory users:

```powershell
Get-ADUser -Identity gordon.stevens -Server za.tryhackme.com -Properties *
```

**Key parameters:**

- `-Identity`: The account name to enumerate.
- `-Properties`: Properties to display (`*` shows all).
- `-Server`: Specify the domain controller (required if not domain-joined).

You can also use the `-Filter` parameter for more control and `Format-Table` for neat output:

```powershell
Get-ADUser -Filter 'Name -like "*stevens"' -Server za.tryhackme.com | Format-Table Name,SamAccountName -AutoSize
```

**Example output:**

| Name             | SamAccountName    |
|------------------|------------------|
| chloe.stevens    | chloe.stevens    |
| samantha.stevens | samantha.stevens |
| janice.stevens   | janice.stevens   |
| gordon.stevens   | gordon.stevens   |

---

## Enumerating Groups

Use `Get-ADGroup` to enumerate AD groups:

```powershell
Get-ADGroup -Identity Administrators -Server za.tryhackme.com
```

To enumerate group membership:

```powershell
Get-ADGroupMember -Identity Administrators -Server za.tryhackme.com
```

**Example output:**

- Domain Admins (group)
- Administrator (user)
- ...

---

## Enumerating AD Objects

For a generic search of any AD objects, use `Get-ADObject`. For example, to find objects changed after a specific date:

```powershell
$ChangeDate = New-Object DateTime(2022, 02, 28, 12, 00, 00)
Get-ADObject -Filter 'whenChanged -gt $ChangeDate' -IncludeDeletedObjects -Server za.tryhackme.com
```

To find accounts with a `badPwdCount` greater than 0 (useful for password spraying):

```powershell
Get-ADObject -Filter 'badPwdCount -gt 0' -Server za.tryhackme.com
```

---

## Enumerating Domains

Retrieve domain information with:

```powershell
Get-ADDomain -Server za.tryhackme.com
```

---

## Altering AD Objects

Some AD-RSAT cmdlets allow you to create or modify AD objects. For example, to change a user's password:

```powershell
Set-ADAccountPassword -Identity gordon.stevens -Server za.tryhackme.com -OldPassword (ConvertTo-SecureString -AsPlainText "old" -Force) -NewPassword (ConvertTo-SecureString -AsPlainText "new" -Force)
```

> **Note:** Change the identity and passwords to match your account.

---

## Benefits

- PowerShell cmdlets can enumerate much more information than `net` commands.
- You can specify the server and domain, even from a non-domain-joined machine.
- Custom cmdlets can be created for specific enumeration tasks.
- AD-RSAT cmdlets allow direct changes to AD objects (e.g., resetting passwords, adding users to groups).

## Drawbacks

- PowerShell activity is often more closely monitored by blue teams.
- Requires AD-RSAT tooling or other scripts, which may be detectable.

