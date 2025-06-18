# Enumerating Active Directory with Microsoft Management Console (MMC)

You should have completed the **Active Directory Basics** room by now, where different AD objects were introduced. In this task, it is assumed that you understand what these objects are.

Connect to **THMJMP1** using RDP and your provisioned credentials from Task 1 to perform this task.

---

## Microsoft Management Console (MMC)

In this task, we will explore our first enumeration method, which is the only method that uses a GUI until the very last task. We will use the Microsoft Management Console (MMC) with the Remote Server Administration Tools' (RSAT) AD Snap-Ins.

- If you use the provided Windows VM (**THMJMP1**), RSAT is already installed.
- If you are using your own Windows machine, install the Snap-Ins as follows:

### Installing RSAT AD Snap-Ins

1. Press **Start**
2. Search for **"Apps & Features"** and press Enter
3. Click **Manage Optional Features**
4. Click **Add a feature**
5. Search for **"RSAT"**
6. Select **"RSAT: Active Directory Domain Services and Lightweight Directory Tools"** and click **Install**

### Starting MMC

- Open the Start menu, search for **Run**, and type `mmc`.
- If your computer is not domain-joined, running MMC normally will not work, as your local account cannot authenticate to the domain.

#### Using Runas for MMC

Use the **Runas** window (from the previous task) to start MMC with your AD credentials. This ensures all MMC network connections use your injected AD credentials.

---

## Attaching the AD RSAT Snap-In

1. Click **File → Add/Remove Snap-in**
2. Select and add all three **Active Directory Snap-ins**
3. Click through any errors or warnings
4. Right-click on **Active Directory Domains and Trusts** → **Change Forest**
    - Enter `za.tryhackme.com` as the Root domain and click **OK**
5. Right-click on **Active Directory Sites and Services** → **Change Forest**
    - Enter `za.tryhackme.com` as the Root domain and click **OK**
6. Right-click on **Active Directory Users and Computers** → **Change Domain**
    - Enter `za.tryhackme.com` as the Domain and click **OK**
7. Right-click on **Active Directory Users and Computers** in the left pane
    - Click **View → Advanced Features**

If everything worked correctly, your MMC should now be pointed to and authenticated against the target domain.

---

## Enumerating AD Structure

### Users and Computers

- Expand the **Active Directory Users and Computers** snap-in.
- Expand the `za` domain to see the initial **Organisational Unit (OU)** structure.
- Explore the **People** directory to see users divided by department OUs.
- Clicking on each OU shows the users in that department.
- Selecting a user allows you to review their properties, attributes, and group memberships.

### Finding Hosts

- Click on **Servers** or **Workstations** to view the list of domain-joined machines.

### Making Changes

- With sufficient permissions, you can use MMC to make changes to AD, such as changing a user's password or adding an account to a group.
- Use the search feature to look for objects.

---

## Benefits

- The GUI provides an excellent holistic view of the AD environment.
- Rapid searching of different AD objects.
- Direct method to view and update AD objects (with privileges).

## Drawbacks

- Requires RDP access to the machine where it is executed.
- Gathering AD-wide properties or attributes cannot be performed efficiently.
