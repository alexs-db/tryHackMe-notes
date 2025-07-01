## Hash Values (Trivial)

As per Microsoft, a hash value is a numeric value of fixed length that uniquely identifies data. It is the result of a hashing algorithm. Here are some of the most common hashing algorithms:

- **MD5 (Message Digest 5, RFC 1321):**  
    Designed by Ron Rivest in 1992, MD5 produces a 128-bit hash value. MD5 is **not** considered cryptographically secure. In 2011, RFC 6151 detailed several attacks against MD5, including hash collisions.

- **SHA-1 (Secure Hash Algorithm 1, RFC 3174):**  
    Invented by the NSA in 1995, SHA-1 produces a 160-bit hash value (40-digit hexadecimal). NIST deprecated SHA-1 in 2011 and banned its use for digital signatures at the end of 2013 due to vulnerabilities. Migration to SHA-2 or SHA-3 is recommended.

- **SHA-2 (Secure Hash Algorithm 2):**  
    Designed by NIST and NSA in 2001 to replace SHA-1. SHA-2 has several variants, with SHA-256 being the most common. SHA-256 produces a 256-bit hash value (64-digit hexadecimal).

A hash is **not** considered cryptographically secure if two files can have the same hash value (a collision).

---

Security professionals use hash values to identify malware samples, suspicious files, and to uniquely reference malicious artifacts.

Ransomware reports often include hashes of malicious files. For examples, see [The DFIR Report](https://thedfirreport.com/) and [Trellix Threat Research Blogs](https://www.trellix.com/en-us/about/newsroom/stories.html).

### Online Hash Lookup Tools

- [VirusTotal](https://www.virustotal.com/)
- [MetaDefender Cloud - OPSWAT](https://metadefender.opswat.com/)

These tools allow you to look up file hashes to check for known malicious files.

---

It is easy to spot a malicious file if you have its hash. However, attackers can trivially modify a file (even by a single bit), resulting in a completely different hash value. With many variants of malware, threat hunting using file hashes as Indicators of Compromise (IOCs) can be challenging.

---

## Example: Changing a File's Hash Value

You can change a file's hash by simply appending a string to it. Here’s how it looks in PowerShell:

**File Hash (Before Modification):**
```powershell
PS C:\Users\THM\Downloads> Get-FileHash .\OpenVPN_2.5.1_I601_amd64.msi -Algorithm MD5

Algorithm Hash                             Path                                                 
--------- ----                             ----                                                 
MD5       D1A008E3A606F24590A02B853E955CF7 C:\Users\THM\Downloads\OpenVPN_2.5.1_I601_amd64.msi
```

**Modify the File:**
```powershell
PS C:\Users\THM\Downloads> echo "AppendTheHash" >> .\OpenVPN_2.5.1_I601_amd64.msi
```

**File Hash (After Modification):**
```powershell
PS C:\Users\THM\Downloads> Get-FileHash .\OpenVPN_2.5.1_I601_amd64.msi -Algorithm MD5

Algorithm Hash                             Path                                                 
--------- ----                             ----                                                 
MD5       9D52B46F5DE41B73418F8E0DACEC5E9F C:\Users\THM\Downloads\OpenVPN_2.5.1_I601_amd64.msi
```

As shown, even a small change results in a completely different hash value.