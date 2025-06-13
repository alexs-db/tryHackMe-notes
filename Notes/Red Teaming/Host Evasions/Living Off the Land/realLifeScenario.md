# Real-Life Scenario: Fileless Malware (Astaroth)

This section introduces a real-world example of malware that uses the techniques discussed in this room.

## Overview

In 2017, the Windows Defender Advanced Threat Protection (ATP) Research Team discovered a fileless malware named **Astaroth**. Fileless malware operates entirely in memory, executing without writing files to disk, making detection and analysis more challenging.

**Astaroth** is an information stealer that targets sensitive user data such as account credentials, keystrokes, and other personal information, sending it to the attacker. It leverages advanced techniques, including:

- Anti-debugging
- Anti-virtualization
- Anti-emulation tricks
- Process hollowing
- NTFS Alternate Data Streams (ADS)
- Living off the land binaries (LOLBins)

## Infection Chain

The initial access is typically achieved through a spam campaign containing a malicious attachment:

1. **LNK File Shortcut**: The victim receives and clicks a shortcut file.
2. **WMIC Command**: Executes to download and run JavaScript code.
3. **BITSadmin Abuse**: Downloads multiple binaries from the command and control (C2) server. In some cases, YouTube channel descriptions are used to hide C2 commands.
4. **ADS Technique**: Uses BITSadmin and ADS to hide binaries for persistence.
5. **Certutil Tool**: Decodes downloaded payloads into DLL files.
6. **Regsvr32 Execution**: Executes the DLL files.

## References

- [Astaroth: Banking Trojan](https://www.cybereason.com/blog/threat-analysis-report-astaroth-banking-trojan)
- [Microsoft Discovers Fileless Malware Campaign Dropping Astaroth Info Stealer](https://www.microsoft.com/security/blog/2019/06/11/dismantling-a-fileless-campaign-bankers-and-infos-stealers-in-living-off-the-land-attacks/)
- [Astaroth malware hides command servers in YouTube channel descriptions](https://www.bleepingcomputer.com/news/security/astaroth-malware-hides-command-servers-in-youtube-channel-descriptions/)