# Introduction to Antivirus: Static Detection

## Overview of AV Detection Approaches

Antivirus (AV) detection can be classified into three main approaches:

1. **Static Detection**
2. **Dynamic Detection**
3. **Heuristic and Behavioral Detection**

---

## Static Detection

Static detection is the simplest type of AV detection, relying on predefined signatures of malicious files. It uses pattern-matching techniques such as:

- Unique strings
- CRC (Checksums)
- Bytecode/Hex sequences
- Cryptographic hashes (e.g., MD5, SHA1)

The AV software compares files in the operating system against a database of signatures. If a match is found, the file is flagged as malicious. This method is effective against static malware.

### Static Detection Flow

In this task, we will use a signature-based detection method to demonstrate how AV products detect malicious files. Note that this technique works only against known malicious files with pre-generated signatures in the database, which must be updated regularly.

We will use **ClamAV** antivirus software to scan malware samples. The ClamAV binary is located at:

```
c:\Program Files\ClamAV\clamscan.exe
```

The malware samples are in a folder on the desktop and include the following files:

- **EICAR**: A test file containing ASCII strings to test AV software.
- **Backdoor 1**: A C# program that establishes a reverse connection using Metasploit shellcode.
- **Backdoor 2**: A C# program that uses process injection and encryption for reverse connections.
- **AV-Check**: A C# program that enumerates AV software (not malicious).
- **notes.txt**: A text file containing a command line (not malicious).

---

## Scanning Malware Samples with ClamAV

Run the following command to scan the malware samples folder:

```cmd
c:\>"c:\Program Files\ClamAV\clamscan.exe" c:\Users\thm\Desktop\Samples
```

### Example Output

```plaintext
C:\Users\thm\Desktop\Samples\AV-Check.exe: OK
C:\Users\thm\Desktop\Samples\backdoor1.exe: Win.Malware.Swrort-9872015-0 FOUND
C:\Users\thm\Desktop\Samples\backdoor2.exe: OK
C:\Users\thm\Desktop\Samples\eicar.com: Win.Test.EICAR_HDB-1 FOUND
C:\Users\thm\Desktop\Samples\notes.txt: OK
```

### Observations

- ClamAV flagged **EICAR** and **Backdoor 1** as malicious.
- **Backdoor 2** was incorrectly identified as non-malicious due to its encryption technique (XOR).
- **AV-Check** and **notes.txt** were correctly identified as non-malicious.

---

## Debugging and Signature Analysis

To debug the scanning process, use the `--debug` flag:

```cmd
c:\>"c:\Program Files\ClamAV\clamscan.exe" --debug <file_to_scan>
```

For example, ClamAV detected **EICAR** using its MD5 signature. To confirm, generate the MD5 hash of the file:

```cmd
c:\>"c:\Program Files\ClamAV\sigtool.exe" --md5 c:\Users\thm\Desktop\Samples\eicar.com
```

Output:

```plaintext
44d88612fea8a8f36de82e1278abb02f:68:eicar.com
```

The hash matches the signature in ClamAV's database.

---

## Creating a Custom Signature Database

ClamAV allows users to create custom databases. To create a signature for **Backdoor 2**:

1. Generate an MD5 hash:

    ```cmd
    C:\Users\thm\Desktop\Samples>"c:\Program Files\ClamAV\sigtool.exe" --md5 backdoor2.exe
    ```

    Output:

    ```plaintext
    75047189991b1d119fdb477fef333ceb:6144:backdoor2.exe
    ```

2. Save the hash to a database file:

    ```cmd
    C:\Users\thm\Desktop\Samples>"c:\Program Files\ClamAV\sigtool.exe" --md5 backdoor2.exe > thm.hdb
    ```

3. Re-scan the file using the custom database:

    ```cmd
    C:\Users\thm\Desktop\Samples>"c:\Program Files\ClamAV\clamscan.exe" -d thm.hdb backdoor2.exe
    ```

    Output:

    ```plaintext
    C:\Users\thm\Desktop\Samples\backdoor2.exe: backdoor2.exe.UNOFFICIAL FOUND
    ```

---

## Yara Rules for Static Detection

Yara is a tool for classifying and detecting malware using rule-based detection. ClamAV supports Yara rules.

### Example Yara Rule

```yara
rule thm_demo_rule {
     meta:
          author = "THM: Intro-to-AV-Room"
          description = "Look at how the Yara rule works with ClamAV"
     strings:
          $a = "C:\\Users\\thm\\source\\repos\\AV-Check\\AV-Check\\obj\\Debug\\AV-Check.pdb"
     condition:
          $a
}
```

### Improved Yara Rule

To reduce false positives, include file type detection:

```yara
rule thm_demo_rule {
     meta:
          author = "THM: Intro-to-AV-Room"
          description = "Look at how the Yara rule works with ClamAV"
     strings:
          $a = "C:\\Users\\thm\\source\\repos\\AV-Check\\AV-Check\\obj\\Debug\\AV-Check.pdb"
          $b = "MZ"
     condition:
          $b at 0 and $a
}
```

---

## Conclusion

Static detection is effective but has limitations, such as being bypassed by modifying binaries. AV vendors continuously improve their databases and detection techniques to address these challenges.