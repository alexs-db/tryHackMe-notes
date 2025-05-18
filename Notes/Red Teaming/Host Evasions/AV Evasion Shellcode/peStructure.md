# Introduction to PE Structure

This task highlights some of the high-level essential elements of the PE (Portable Executable) data structure for Windows binaries.

## What is PE?

The **Portable Executable (PE)** format is the standard file format for executables, object code, DLLs, and others in Windows. It organizes executable code and data on disk, allowing Windows components (like loaders) to parse and execute the file.

PE files are used for EXE, DLL, and object code files, and the structure is consistent across both x86 and x64 architectures.

## Key Sections of the PE Structure

A PE file is divided into several sections, each serving a specific purpose:

- **.text**: Contains the actual code of the program.
- **.data**: Holds initialized and defined variables.
- **.bss**: Stores uninitialized data (declared variables without assigned values).
- **.rdata**: Contains read-only data.
- **.edata**: Holds exportable objects and related table information.
- **.idata**: Contains imported objects and related table information.
- **.reloc**: Stores image relocation information.
- **.rsrc**: Contains external resources (images, icons, embedded binaries, manifest files, etc.).

The **PE Header** contains metadata, pointers, and links to address sections in memory. The **Data section** includes containers for executable code, resources, library links, data variables, and more.

## Overview of the PE Loading Process

When the Windows loader runs a PE file, it follows these general steps:

1. **Parse Header Sections**: DOS, Windows, and optional headers provide information about the EXE file, such as:
    - Magic number ("MZ") indicating an EXE file.
    - File signatures.
    - Target CPU architecture (x86 or x64).
    - Creation timestamp.
2. **Parse Section Table**: Determines the number of sections in the file.
3. **Map Contents into Memory**: Based on the EntryPoint address and ImageBase offset (RVA: Relative Virtual Address).
4. **Load Imports and DLLs**: Loads required objects into memory.
5. **Locate EntryPoint**: Begins execution at the main function.

## Why Learn About PE?

Understanding the PE structure is crucial for:

- **Packing/Unpacking**: Techniques often require knowledge of the PE format.
- **Malware Analysis & AV Evasion**: AV software and analysts inspect PE headers and sections to detect malicious code.

### Storing Shellcode in PE Sections

- **Local variable in main function**: Stored in the `.text` section.
- **Global variable**: Stored in the `.data` section.
- **Embedded in an icon image**: Appears in the `.rsrc` section.
- **Custom section**: You can add a new section for shellcode.

## Tools: PE-Bear

The provided VM includes **PE-Bear**, a GUI tool for analyzing PE files (headers, sections, etc.).

- **To load a file**: Go to `File -> Load PEs` (Ctrl + O).
- **Example**: Load `thm-intro2PE.exe` from `c:\Tools\PE files\thm-intro2PE.exe` to explore its structure.

## Further Reading

- **THM Rooms**:  
    - Windows Internals  
    - Dissecting PE Headers
- **Microsoft Docs**: [PE Format Documentation](https://learn.microsoft.com/en-us/windows/win32/debug/pe-format)

---

Now, try loading the sample file in PE-Bear and explore the headers and sections discussed above.