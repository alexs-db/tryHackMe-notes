# Antivirus Engines

An antivirus (AV) engine is responsible for identifying and removing malicious code and files. Effective AV software implements a robust core that accurately and efficiently analyzes malicious files. It should also support various file types, including archive files, by self-extracting and inspecting all compressed files.

Most AV products share common features, though they may be implemented differently. These features include:

- Scanner
- Detection techniques
- Compressors and archives
- Unpackers
- Emulators

## Scanner

The scanner is a core feature of most AV products. It allows AV software to run scans in real-time or on-demand. This feature is accessible via a graphical user interface (GUI) or the command line, enabling users to check files or directories as needed. 

The scanning feature must support detection of the most common malicious file types and may also include additional scanning capabilities, such as:

- Vulnerability scanning
- Email scanning
- Windows memory scanning
- Windows Registry scanning

## Detection Techniques

Detection techniques are used to identify malicious files. Common techniques include:

- **Signature-based detection**: A traditional method that searches for predefined malicious patterns and signatures within files.
- **Heuristic detection**: An advanced method that uses behavioral analysis to identify suspicious files.
- **Dynamic detection**: A technique that monitors system calls and APIs, analyzing files in an isolated environment.

A good AV engine should detect malicious files accurately and quickly, minimizing false positives. Inaccurate detection or misclassification of files can lead to ineffective protection.

## Compressors and Archives

AV software must handle compressed or archived files, such as ZIP, TGZ, 7z, XAR, and RAR. Malicious code often hides in compressed files to evade detection. Therefore, AV software must decompress and scan all files within an archive before they are accessed by the user.

## PE Parsing and Unpackers

Malware often uses techniques like packing to compress or encrypt malicious code, making static analysis more difficult. AV software must include unpackers to detect and unpack known packers (e.g., UPX, Armadillo, ASPack) before runtime.

Additionally, AV software should include a Windows Portable Executable (PE) header parser. Parsing the PE headers of executable files helps distinguish between malicious and legitimate software. The PE file format contains information such as object code, DLLs, icons, fonts, and core dumps.

## Emulators

An emulator is an AV feature that performs further analysis on suspicious files. It runs suspect files (e.g., EXE, DLL, PDF) in a virtualized and controlled environment, monitoring their behavior during execution. Artifacts collected during emulation may include:

- API calls
- Memory dumps
- Filesystem modifications
- Log events
- Running processes
- Web requests

The emulator stops execution once sufficient artifacts are collected to determine whether the file is malicious.

## Other Common Features

Additional features commonly found in AV products include:

- A self-protection driver to prevent malware from attacking the AV software itself.
- Firewall and network inspection functionality.
- Command-line and graphical interface tools.
- A daemon or service for continuous operation.
- A management console for centralized control.
