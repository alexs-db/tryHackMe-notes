# Detection Techniques in Antivirus

## Static Detection

The concept of static detection is relatively simple. In this section, we will discuss the different types of detection techniques.

---

## Dynamic Detection

The dynamic detection approach is advanced and more complicated than static detection. It focuses on checking files at runtime using different methods. The following diagram shows the dynamic detection scanning flow:

### Dynamic Detection Flow

1. **Monitoring Windows APIs**  
    The detection engine inspects Windows application calls and monitors Windows API calls using Windows Hooks.

2. **Sandboxing**  
    A sandbox is a virtualized environment used to run malicious files separated from the host computer. This is usually done in an isolated environment to analyze how the malicious software acts in the system. Once confirmed as malicious, a unique signature and rule are created based on the binary's characteristics. Finally, a new update is pushed into the cloud database for future use.

### Drawbacks of Dynamic Detection

- Requires executing and running the malicious software for a limited time in the virtual environment to protect system resources.
- Malware developers implement techniques to bypass dynamic detection, such as:
  - Ensuring the software does not work within virtual or simulated environments.
  - Checking if the system spawns a real process before running malicious activities.
  - Delaying execution to avoid detection.

For more information about sandbox evasion, check the THM room: **Sandbox Evasion**.

---

## Heuristic and Behavioral Detection

Heuristic and behavioral detection have become essential in modern antivirus (AV) products. Modern AV software relies on this type of detection to identify malicious software.

### Heuristic Analysis

1. **Static Heuristic Analysis**  
    - Decompiles (if possible) and extracts the source code of the malicious software.  
    - Compares the extracted source code to well-known virus source codes stored in a heuristic database.  
    - Flags the code as malicious if a match meets or exceeds a threshold percentage.

2. **Dynamic Heuristic Analysis**  
    - Based on predefined behavioral rules.  
    - Security researchers analyze suspicious software in isolated environments.  
    - Behavioral rules are created to match the software's malicious activities within a target machine.

### Examples of Behavioral Rules

- If a process tries to interact with the `LSASS.exe` process, which contains users' NTLM hashes, Kerberos tickets, and more.
- If a process opens a listening port and waits to receive commands from a Command and Control (C2) server.

The following diagram shows the heuristic and behavioral detection scanning flow:

---

## Summing Up Detection Methods

Modern AV software combines various features and detection techniques to implement its AV engine. Below is an example of how these components work together:

### Detection Methods Summary

1. A suspicious `Foobar.zip` file is passed to the AV software for scanning.
2. The software recognizes it as a compressed file (`.zip`) and applies an un-archiver feature to extract the files (`Foobar.exe`).
3. It identifies the file type to determine the appropriate module to work with.
4. Performs a PE parsing operation to pull the binary's information and other characteristic features.
5. Checks whether the file is packed; if so, it unpacks the code.
6. Passes the collected information and the binary to the AV engine, which determines if it is malicious and provides the result.

--- 