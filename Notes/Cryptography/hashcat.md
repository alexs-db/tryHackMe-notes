Hashcat is a versatile password-cracking tool. To crack a hash, you can use this syntax: hashcat -m <hash_type> -a <attack_mode> hashfile wordlist.
## Explanation of Options

- **`-m <hash_type>`**: Specifies the hash type in numeric form, like `-m 1000` for NTLM. You can find codes for different types by checking the documentation (`man hashcat`).
- **`-a <attack_mode>`**: Defines the type of attack. For instance, `-a 0` (straight attack) tries each password from the wordlist sequentially.
- **`hashfile`**: Path to the file containing the hash to be cracked.
- **`wordlist`**: The file path for the wordlist, such as `rockyou.txt`, which contains common passwords.

### Example

```sh
hashcat -m 3200 -a 0 hash.txt /usr/share/wordlists/rockyou.txt
```

This command tries to crack a Bcrypt hash using passwords from the `rockyou.txt` wordlist.