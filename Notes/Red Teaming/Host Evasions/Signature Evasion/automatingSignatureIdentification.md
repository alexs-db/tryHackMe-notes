## Automating Signature Identification

The manual process described previously can be time-consuming. To streamline this, we can use scripts to automate splitting bytes over intervals.

### Find-AVSignature

`Find-AVSignature` is a PowerShell script that splits a provided range of bytes through a given interval.

```powershell
PS C:\> . .\Find-AVSignature.ps1
PS C:\> Find-AVSignature
```

You will be prompted for parameters:

```
cmdlet Find-AVSignature at command pipeline position 1
Supply values for the following parameters:
StartByte: 0
EndByte: max
Interval: 1000

Do you want to continue?
This script will result in 1 binaries being written to "C:\Users\TryHackMe"!
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
```

This script reduces manual work but still requires setting an appropriate interval and only observes strings in the binary when dropped to disk, not using the full anti-virus engine.

---

To address these limitations, we can use FOSS (Free and Open-Source Software) tools that leverage AV engines directly, such as **DefenderCheck**, **ThreatCheck**, and **AMSITrigger**. This section focuses on **ThreatCheck** and briefly covers **AMSITrigger**.

---

### ThreatCheck

**ThreatCheck** is a fork of DefenderCheck and is widely used for signature identification. It uses multiple AV engines to scan split binaries and reports where bad bytes are detected.

> **Note:** ThreatCheck does not provide pre-compiled releases. A compiled version is available at `C:\Users\Administrator\Desktop\Tools` on the attached machine.

#### Basic Usage

```shell
C:\> ThreatCheck.exe --help
    -e, --engine    (Default: Defender) Scanning engine. Options: Defender, AMSI
    -f, --file      Analyze a file on disk
    -u, --url       Analyze a file from a URL
    --help          Display this help screen.
    --version       Display version information.
```

For most cases, supply a file and optionally an engine. Use `AMSI` engine for AMSI-related scans.

#### Example

```shell
C:\> ThreatCheck.exe -f Downloads\Grunt.bin -e AMSI
```

Sample output:

```
[+] Target file size: 31744 bytes
[+] Analyzing...
[!] Identified end of bad bytes at offset 0x6D7A
00000000   65 00 22 00 3A 00 22 00  7B 00 32 00 7D 00 22 00   e·"·:·"·{·2·}·"·
...
```

No further configuration is needed. To efficiently use ThreatCheck, identify bad bytes, recursively break them, and rerun the tool until no signatures are found.

> **Note:** False positives may occur, requiring manual analysis. This is discussed further in Task 4.

---

### AMSITrigger

As discussed in Runtime Detection Evasion, AMSI signatures are harder to identify. ThreatCheck does not support some file types (e.g., PowerShell) that **AMSITrigger** does.

**AMSITrigger** uses the AMSI engine to scan PowerShell scripts and reports suspicious code sections.

> **Note:** AMSITrigger provides pre-compiled releases on GitHub and is also available on the Desktop of the attached machine.

#### Usage

```shell
C:\> amsitrigger.exe --help
        -i, --inputfile=VALUE       Powershell filename
        -u, --url=VALUE             URL (e.g., https://10.1.1.1/Invoke-NinjaCopy.ps1)
        -f, --format=VALUE          Output Format:
                                                                    1 - Only show Triggers
                                                                    2 - Show Triggers with Line numbers
                                                                    3 - Show Triggers inline with code
                                                                    4 - Show AMSI calls (xmas tree mode)
        -d, --debug                 Show Debug Info
        -m, --maxsiglength=VALUE    Maximum signature length (default: 2048)
        -c, --chunksize=VALUE       Chunk size to send to AMSIScanBuffer (default: 4096)
        -h, -?, --help              Show Help
```

For typical use, supply a file and preferred output format.

#### Example

```powershell
PS C:\> .\amsitrigger.exe -i bypass.ps1 -f 3
```

Sample output:

```
[Ref].Assembly.GetType('System.Management.Automation.AmsiUtils').GetField('amsiInitFailed','NonPublic,Static').SetValue($null,$true)
```

---

In the next task, we will discuss how to use the information from these tools to break signatures.