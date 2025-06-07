# Static Property-Based Signatures

Detection engines and analysts may use various indicators beyond simple strings or static signatures to identify files. Signatures can be attached to several file properties, such as file hash, entropy, author, name, or other identifiable information. These properties are often used in rule sets like **YARA** or **Sigma**.

Some properties are easily manipulated, while others—especially in pre-compiled, closed-source applications—are more difficult to alter.

This section discusses manipulating file hashes and entropy for both open-source and closed-source applications.

> **Note:** Other properties, such as PE headers or module properties, can also serve as indicators. However, since these often require an agent or additional detection measures, they are not covered here to keep the focus on signatures.

---

## File Hashes

A **file hash** (checksum) uniquely identifies a file and is commonly used to verify authenticity or detect known malicious files. Any modification to a file changes its hash.

- **Open-source applications:** If you have access to the source code, modify any part and recompile to generate a new hash.
- **Closed-source or signed applications:** Use **bit-flipping**.

### Bit-Flipping

Bit-flipping is a technique where each bit in a file is flipped and tested to find a viable variant. Flipping a single bit changes the file's hash and signature while maintaining functionality.

Below is an example Python script for bit-flipping:

```python
import sys

orig = list(open(sys.argv[1], "rb").read())

i = 0
while i < len(orig):
    current = list(orig)
    current[i] = chr(ord(current[i]) ^ 0xde)
    path = "%d.exe" % i

    output = "".join(str(e) for e in current)
    open(path, "wb").write(output)
    i += 1

print("done")
```

After generating bit-flipped variants, search for intact unique properties (e.g., valid certificates). For example, use `signtool` to verify signed files:

```batch
FOR /L %%A IN (1,1,10000) DO (
    signtool verify /v /a flipped\%%A.exe
)
```

This technique can be effective but is time-consuming and only works until the new hash is discovered.

---

## Entropy

**Entropy** measures the randomness of data in a file and is used to detect hidden data or suspicious scripts. EDRs and scanners often use entropy as part of their detection logic.

- **Obfuscated scripts** often have higher entropy due to random identifiers.
- To lower entropy, replace random identifiers with English words (e.g., change `q234uf` to `nature`).

### Entropy Examples

- **Standard English paragraph:**  
  Shannon entropy: `4.59`

- **Script with random identifiers:**  
  Shannon entropy: `5.34`

A "suspicious" entropy value is typically greater than `6.8`. The difference between random values and English text becomes more pronounced in larger files.

> Entropy is rarely used alone; it supports other indicators. For example, the entropy of legitimate tools and exploits can be similar.

### Real-World Example

A white paper, *An Empirical Assessment of Endpoint Detection and Response Systems against Advanced Persistent Threats Attack Vectors*, shows SentinelOne detecting a DLL due to high entropy, specifically from AES encryption.

---