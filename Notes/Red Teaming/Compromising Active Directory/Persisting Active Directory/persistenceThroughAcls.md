# Persisting Access to All Protected AD Groups

Sometimes, persisting access to normal AD groups isn't enough. What if you want persistence across all protected groups simultaneously? This is where AD group templates come into play.

## Persisting Through AD Group Templates

While you could add your account to every privileged group, defenders can still remove your membership. For better persistence, inject your account into the templates that generate default groups. When the template refreshes, your access is restored—even if defenders remove you.

### The AdminSDHolder Container

The **AdminSDHolder** container exists in every AD domain. Its Access Control List (ACL) acts as a template for all protected groups, such as:

- Domain Admins
- Administrators
- Enterprise Admins
- Schema Admins

A process called **SDProp** copies the ACL from AdminSDHolder to all protected groups every 60 minutes. By modifying AdminSDHolder’s ACL, you can grant yourself permissions on all protected groups. If defenders aren’t aware of this, your permissions will keep reappearing, making it difficult to track the source.

## Persisting with AdminSDHolder

To persist via AdminSDHolder:

1. **Open MMC as Administrator:**
    - RDP into `THMWRK1` with low-privileged credentials.
    - Use `runas` to open a terminal as Administrator:
      ```shell
      runas /netonly /user:thmchilddc.tryhackme.loc\Administrator cmd.exe
      ```
2. **Launch MMC and Add Snap-In:**
    - Open MMC.
    - Add the "Active Directory Users and Computers" snap-in.
    - Enable "Advanced Features" (View → Advanced Features).

3. **Locate AdminSDHolder:**
    - Navigate to: `Domain → System → AdminSDHolder`.

4. **Modify Security:**
    - Right-click AdminSDHolder → Properties → Security.
    - Add your low-privileged user.
    - Grant **Full Control**.
    - Apply and confirm changes.

## Triggering SDProp

By default, SDProp runs every 60 minutes. To trigger it manually:

```powershell
Import-Module .\Invoke-ADSDPropagation.ps1
Invoke-ADSDPropagation
```
*(Assuming the script is in `C:\Tools\`)*

After running, check the security permissions of a protected group (e.g., Domain Admins). Your user should have full control. If you remove your user and rerun the script, your permissions will be restored.

> **Note:** Having full control over a group does not automatically add you as a member, but you can add yourself using your new permissions.

## Defender Challenges

Combining this persistence with group nesting can make it extremely difficult for defenders to remove your access. Unless they realize the AdminSDHolder ACL is being abused, your permissions will keep returning. For maximum persistence, you could grant full control to the **Domain Users** group in AdminSDHolder, giving all users control over protected groups. Combined with DC Sync, defenders would need to reset every credential to fully remove your access.
