# Credentials Harvesting: Local Administrator Password Solution (LAPS)

This task explains how to enumerate and obtain a local administrator password in an Active Directory environment when LAPS is configured and enabled.

---

## Group Policy Preferences (GPP)

Windows includes a built-in Administrator account, but managing its password across many computers is challenging. Microsoft introduced Group Policy Preferences (GPP) to help automate this process.

GPP allows administrators to create domain policies with embedded credentials. When deployed, GPP creates XML files in the SYSVOL folder, which is accessible to all authenticated domain users. Originally, these XML files stored passwords encrypted with AES-256. However, Microsoft accidentally published the private key, making it possible for attackers to decrypt these passwords. Tools like `Get-GPPPassword` can extract these credentials.

---

## Local Administrator Password Solution (LAPS)

To address GPP's security issues, Microsoft introduced LAPS in 2015. LAPS securely manages local administrator passwords by storing them in Active Directory attributes:

- `ms-mcs-AdmPwd`: Stores the clear-text local admin password.
- `ms-mcs-AdmPwdExpirationTime`: Stores the password expiration time.

LAPS uses `admpwd.dll` to change and update these attributes.

---

## Enumerating LAPS

To check if LAPS is installed on a target machine, look for the `admpwd.dll` file:

```shell
dir "C:\Program Files\LAPS\CSE"
```

Sample output:

```
 Directory of C:\Program Files\LAPS\CSE

05/05/2021  07:04 AM           184,232 AdmPwd.dll
```

---

### Listing LAPS PowerShell Cmdlets

Use PowerShell to list available LAPS cmdlets:

```powershell
Get-Command *AdmPwd*
```

Sample output:

```
Cmdlet          Find-AdmPwdExtendedRights
Cmdlet          Get-AdmPwdPassword
Cmdlet          Reset-AdmPwdPassword
Cmdlet          Set-AdmPwdAuditing
Cmdlet          Set-AdmPwdComputerSelfPermission
Cmdlet          Set-AdmPwdReadPasswordPermission
Cmdlet          Set-AdmPwdResetPasswordPermission
Cmdlet          Update-AdmPwdADSchema
```

---

### Finding OUs with LAPS Rights

Identify which AD Organizational Units (OUs) have "All extended rights" for LAPS using:

```powershell
Find-AdmPwdExtendedRights -Identity THMorg
```

Sample output:

```
ObjectDN                                      ExtendedRightHolders
OU=THMorg,DC=thm,DC=red                       {THM\THMGroupReader}
```

---

### Enumerating Group Members

Check group membership for LAPS rights:

```powershell
net groups "THMGroupReader"
```

Sample output:

```
Members
-------------------------------------------------------------------------------
bk-admin
```

Check user group memberships:

```powershell
net user test-admin
```

Sample output (excerpt):

```
Global Group memberships     *Domain Users         *Domain Admins
                             *THMGroupReader       *Enterprise Admins
```

---

## Retrieving the LAPS Password

After compromising or impersonating a user with the right permissions (e.g., `bk-admin`), retrieve the LAPS password:

```powershell
Get-AdmPwdPassword -ComputerName creds-harvestin
```

Sample output:

```
ComputerName         DistinguishedName                             Password           ExpirationTimestamp
CREDS-HARVESTIN      CN=CREDS-HARVESTIN,OU=THMorg,DC=thm,DC=red    FakePassword       2/11/2338 11:05:2...
```

---

> **Note:** In real-world environments, LAPS is typically enabled only on specific machines. Proper enumeration is required to identify target computers and users with the necessary permissions. Tools like LAPSToolkit (e.g., in `C:\Tool`) can assist with this process.
