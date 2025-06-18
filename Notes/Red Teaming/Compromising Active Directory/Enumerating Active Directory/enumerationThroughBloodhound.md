# Enumerating Active Directory with Bloodhound

Bloodhound is a powerful Active Directory (AD) enumeration tool that revolutionized AD analysis when it was released in 2016. It enables both attackers and defenders to visualize AD environments as graphs, revealing complex attack paths that are difficult to spot using traditional list-based approaches.

---

## Bloodhound History

Bloodhound changed the way red teamers and attackers approach AD enumeration. Its graph-based methodology allows users to map relationships and permissions between AD objects, making it easier to identify potential attack paths. Microsoft even integrated similar capabilities into its Advanced Threat Protection solution.

> "Defenders think in lists, Attackers think in graphs." – Unknown

By thinking in graphs, attackers can plan multi-stage attacks: first, they collect AD data (often noisily), then analyze it offline to find optimal attack paths, and finally execute targeted attacks quickly. This approach has also been adopted by blue teams to better understand and defend their environments.

---

## Sharphound

Sharphound is the data collection component of Bloodhound. While the terms are sometimes used interchangeably, Sharphound gathers AD data, and Bloodhound provides the GUI for analysis.

### Sharphound Collectors

- **Sharphound.ps1**: PowerShell script (no longer updated, but useful for in-memory execution).
- **Sharphound.exe**: Windows executable.
- **AzureHound.ps1**: PowerShell script for Azure AD enumeration.

> **Note:** Ensure your Bloodhound and Sharphound versions match for compatibility. This guide uses Bloodhound v4.1.0.

### Running Sharphound

Sharphound binaries are typically detected by AV/EDR. To avoid this, use a non-domain-joined Windows machine where you control AV settings. On the THMJMP1 machine, Sharphound binaries are in `C:\Tools\`.

Example usage:

```powershell
SharpHound.exe --CollectionMethods <Methods> --Domain za.tryhackme.com --ExcludeDCs
```

**Parameters:**
- `CollectionMethods`: What data to collect (`Default`, `All`, `Session`, etc.).
- `Domain`: The target domain.
- `ExcludeDCs`: Avoids touching domain controllers to reduce detection.

**Example session:**

```powershell
copy C:\Tools\Sharphound.exe ~\Documents\
cd ~\Documents\
SharpHound.exe --CollectionMethods All --Domain za.tryhackme.com --ExcludeDCs
```

After execution, a timestamped ZIP file will be created in the working directory.

---

## Using Bloodhound

Bloodhound uses Neo4j as its backend database. To start:

1. **Start Neo4j:**
    ```bash
    neo4j console start
    ```
2. **Launch Bloodhound:**
    ```bash
    bloodhound --no-sandbox
    ```
3. **Login:** Default Neo4j credentials are `neo4j:neo4j`.
4. **Import Data:** Use SCP to transfer the Sharphound ZIP from the Windows host, then drag and drop it into Bloodhound.

---

## Exploring Attack Paths

Bloodhound visualizes AD objects (nodes) and their relationships (edges). You can:

- Search for specific users or groups.
- View node details: overview, properties, group membership, admin rights, execution rights, and control rights.
- Run built-in analysis queries (e.g., "Find all Domain Admins").
- Explore attack paths by selecting a start and end node.

**Example:** If your account is a member of `DOMAIN USERS`, and a `T1 ADMIN` has an active session on a workstation you can access, you may be able to escalate privileges by:

1. RDP into the workstation.
2. Escalate privileges locally.
3. Harvest credentials (e.g., with Mimikatz).
4. Use harvested hashes to move laterally.

Attack paths can be complex and may require multiple steps.

---

## Session Data Considerations

AD structure changes infrequently, but session data (active logons) changes constantly. For accurate session data:

- Run Sharphound with `All` at the start of an assessment.
- Run Sharphound with `Session` at least twice daily (e.g., mid-morning and after lunch).
- Clear old session data in Bloodhound before importing new session runs.

---

## Benefits & Drawbacks

**Benefits:**
- GUI-based AD enumeration.
- Visualizes attack paths.
- Provides deep insights into AD objects.

**Drawbacks:**
- Sharphound execution is noisy and often detected by AV/EDR.

---

**References:**
- [Bloodhound Documentation](https://bloodhound.readthedocs.io/)

