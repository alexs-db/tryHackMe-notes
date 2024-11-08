Basic Syntax:
# John the Ripper Notes

## Command Structure
```
john [options] [file path]
```
- `john`: Starts John the Ripper.
- `[options]`: Specifies options or settings.
- `[file path]`: Path to the file containing the hash to crack (if in the same directory, just the filename).

## Automatic Cracking
**Command:**
```
john --wordlist=[path to wordlist] [path to file]
```
- `--wordlist=[path]`: Use wordlist mode with specified file.

**Example:**
```
john --wordlist=/usr/share/wordlists/rockyou.txt hash_to_crack.txt
```

## Hash Identification
If John Can’t Detect Hash Automatically:
- Use tools like `hash-identifier` to identify the hash type.
- To download: 
    ```
    wget https://gitlab.com/kalilinux/packages/hash-identifier/-/raw/kali/master/hash-id.py
    ```
- Run with: 
    ```
    python3 hash-id.py
    ```
    Then input the hash to get possible types.

## Format-Specific Cracking
**Command:**
```
john --format=[format] --wordlist=[path to wordlist] [path to file]
```
- `--format=[format]`: Specify the hash format.

**Example:**
```
john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt hash_to_crack.txt
```

**Note:** For standard formats (e.g., MD5), prefix with `raw-` if required. List available formats with:
```
john --list=formats
```
Or filter:
```
john --list=formats | grep -iF "md5"
```

## Authentication Hashes (NTLM/NTHash)
NTLM/NTHash: Hash format used by modern Windows OS to store passwords (formerly known as LM in older systems).

**Background:** NT refers to "New Technology," which began with Windows NT OS; now it’s the standard Windows OS type.

**Where Hashes are Stored:**
- **SAM Database:** Security Account Manager (SAM) in Windows stores usernames and hashed passwords.
- **NTDS.dit:** Active Directory database also contains NTLM hashes.

**How to Obtain NTLM Hashes:**
- Use tools like Mimikatz to dump the SAM database.
- Retrieve hashes from NTDS.dit for Active Directory setups.

**Cracking vs. Pass-the-Hash:**
- **Pass-the-Hash Attack:** Often a practical method for privilege escalation without cracking.
- **Hash Cracking:** Useful if there’s a weak password policy, and brute-forcing is feasible.

## Cracking Hashes from /etc/shadow
`/etc/shadow`: Linux file that stores password hashes and other data (last password change, expiration info).
- **Access:** Only accessible by root, so elevated privileges are required to view and crack these hashes.

**Unshadowing:**
- **Purpose:** Combines `/etc/passwd` and `/etc/shadow` data so John can interpret it.
- **Command Syntax:** `unshadow [path to passwd] [path to shadow]`
- **Example:** `unshadow local_passwd local_shadow > unshadowed.txt`
- **Note:** You can use either the entire files or just relevant lines from each (e.g., only for root).

**Cracking with John:**
- **Command:** `john --wordlist=/usr/share/wordlists/rockyou.txt unshadowed.txt`
- **Specify format if needed:** e.g., `--format=sha512crypt`

## Single Crack Mode
**Purpose:** Cracks hashes by modifying the username to generate possible passwords (word mangling).

**Word Mangling:** John modifies the username with numbers, capitalization, and symbols (e.g., "Markus1", "MArkus", "Markus$") to create a list of likely passwords.

**GECOS Field:**
- **Details:** Found in the fifth field of /etc/passwd, the GECOS field stores user info like name and phone number.
- John uses this additional data to enhance word mangling and create targeted password variations.

**Using Single Crack Mode:**
- **Syntax:** `john --single --format=[format] [path to file]`
- **Example:** `john --single --format=raw-sha256 hashes.txt`

**File Format Requirement:**
- **Modification:** Add the username before each hash to help John identify the target.
- **Example Change:** `1efee03cdcb96d90ad48ccc7b8666033 ➔ mike:1efee03cdcb96d90ad48ccc7b8666033`

## Custom Rules
**Purpose:** Custom rules enable you to create dynamic password patterns for cracking based on known password structures (e.g., Polopassword1!).

**Complexity Patterns:** Many users follow predictable patterns with complex passwords, like capitalizing the first letter or adding symbols and numbers at the end.

**Creating Custom Rules:**
- **File:** Custom rules are added to the john.conf file (usually in /etc/john/john.conf).
- **Structure:**
  - Define Rule Name: `[List.Rules:RuleName]` sets the rule’s identifier.
  - Modifiers: Common ones include:
    - `Az`: Appends specified characters to the end.
    - `A0`: Prepends specified characters.
    - `c`: Capitalizes specific characters by position.
  - Character Sets: Defined with square brackets, e.g.:
    - `[0-9]` for numbers, `[A-Z]` for uppercase letters, `[!£$%@]` for symbols.

**Example:**
To match Polopassword1!:
```
[List.Rules:PoloPassword]
cAz"[0-9][!£$%@]"
```
- `c`: Capitalizes the first letter.
- `Az`: Appends a number and symbol to the end.

**Using the Rule:**
- **Command:** `john --wordlist=[path to wordlist] --rule=PoloPassword [path to file]`

Jumbo John includes many pre-made rules, helpful as examples or starting points if syntax issues arise.

## Cracking Password-Protected Zip Files
Tool: `zip2john` is used to convert a Zip file into a hash format readable by John.

**Usage:**
Basic Syntax:
```
zip2john [options] [zip file] > [output file]
```
- **Options:** Generally optional; used for checksum settings.
- **Zip File:** Path to the target Zip file.
- **Output File:** Where the generated hash output will be saved.

**Example:**
```
zip2john zipfile.zip > zip_hash.txt
```

**Cracking:**
Command:
```
john --wordlist=/usr/share/wordlists/rockyou.txt zip_hash.txt
```
Here, `zip_hash.txt` is the file generated by `zip2john` and is used as input for John. This method enables you to attempt cracking Zip file passwords using familiar syntax once the hash format is prepared with `zip2john`.

## Cracking Password-Protected RAR Files
Tool: `rar2john` is used to transform a RAR archive into a format that John the Ripper can process.

**Usage:**
Basic Syntax:
```
rar2john [rar file] > [output file]
```
- **RAR File:** The path to the target RAR archive.
- **Output File:** A file where the command’s output will be saved.

**Example:**
```
/opt/john/rar2john rarfile.rar > rar_hash.txt
```

**Cracking:**
Command:
```
john --wordlist=/usr/share/wordlists/rockyou.txt rar_hash.txt
```
Here, `rar_hash.txt` is the generated hash file from `rar2john`, which John can now attempt to crack. This method leverages `rar2john` for RAR files, working similarly to `zip2john` by preparing the hash format needed for John the Ripper to carry out the password attack.

## Cracking SSH Key Passwords with John the Ripper
Goal: Crack the password of an SSH private key `id_rsa` to enable SSH authentication.

Using the `ssh2john` Tool: Converts the `id_rsa` file into a hash format that John can process.

If `ssh2john` is not installed, use the Python script `ssh2john.py`:
- On AttackBox: `/opt/john/ssh2john.py`
- On Kali: `/usr/share/john/ssh2john.py`

**Syntax:**
```
ssh2john [id_rsa file] > [output file]
```
- `[id_rsa file]`: Path to the SSH private key file you want to hash.
- `[output file]`: Name of the output file where the hash will be saved.

**Example Command:**
```
python3 /opt/john/ssh2john.py id_rsa > id_rsa_hash.txt
```

**Cracking the Password:**
Use John the Ripper to crack the password:
```
john --wordlist=/usr/share/wordlists/rockyou.txt id_rsa_hash.txt
```
This approach allows John to attempt cracking the password for the SSH key using the specified wordlist.
