# What is LOLBAS?

**LOLBAS** stands for **Living Off the Land Binaries And Scripts**. The project's primary goal is to gather and document Microsoft-signed and built-in tools that can be used as Living Off the Land techniques, including binaries, scripts, and libraries.

The LOLBAS project is a community-driven repository collecting binaries, scripts, and libraries that could be leveraged for red team purposes. It allows searching based on binaries, functions, scripts, and ATT&CK information.

If you are interested in more details about the project, visit the [LOLBAS website](https://lolbas-project.github.io/). The website provides a convenient search bar to query all available data:

- To search for a binary, enter its name.
- To search for a specific function, prefix it with `/` (e.g., `/execute`).
- To search by type, use `#` followed by the type name.

**Types included in the project:**
- Script
- Binary
- Libraries
- OtherMSBinaries

## Tools Criteria

For a tool to be accepted as a "Living Off the Land" technique in the LOLBAS project, it must meet the following criteria:

- Microsoft-signed file native to the OS or downloadable from Microsoft.
- Has additional, interesting, unintended functionality not covered by known use cases.
- Benefits an APT (Advanced Persistent Threat) or Red Team engagement.

If you discover a binary that meets these criteria, you can submit your finding via the [GitHub repo contribution page](https://github.com/LOLBAS-Project/LOLBAS).

## Interesting Functionalities

The LOLBAS project accepts tool submissions that fit one or more of the following functionalities:

- Arbitrary code execution
- File operations (downloading, uploading, copying)
- Compiling code
- Persistence (e.g., hiding data in Alternate Data Streams (ADS), executing at logon)
- UAC bypass
- Dumping process memory
- DLL injection