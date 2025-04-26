# Portable Executable (PE) Format

Executables and applications are a large portion of how Windows internals operate at a higher level. The PE (Portable Executable) format defines the information about the executable and stored data. It also defines the structure of how data components are stored.

The PE format is an overarching structure for executable and object files. It consists of the PE (Portable Executable) and COFF (Common Object File Format) files.

PE data is most commonly seen in the hex dump of an executable file. Below, we will break down a hex dump of `calc.exe` into the sections of PE data.

## Structure of PE Data

The structure of PE data is broken into seven components:

### 1. DOS Header
The DOS Header defines the type of file. The MZ DOS header defines the file format as `.exe`. The DOS header can be seen in the hex dump section below:

```
Offset(h) 00 01 02 03 04 05 06 07 08 09 0A 0B 0C 0D 0E 0F
00000000  4D 5A 90 00 03 00 00 00 04 00 00 00 FF FF 00 00  MZ..........ÿÿ..
00000010  B8 00 00 00 00 00 00 00 40 00 00 00 00 00 00 00  ¸.......@.......
...
```

### 2. DOS Stub
The DOS Stub is a program run by default at the beginning of a file that prints a compatibility message. This does not affect the functionality of the file for most users.

The DOS stub prints the message: `This program cannot be run in DOS mode.` The DOS stub can be seen in the hex dump section below:

```
00000040  0E 1F BA 0E 00 B4 09 CD 21 B8 01 4C CD 21 54 68  ..º..´.Í!¸.LÍ!Th
00000050  69 73 20 70 72 6F 67 72 61 6D 20 63 61 6E 6E 6F  is program canno
...
```

### 3. PE File Header
The PE File Header provides PE header information of the binary. It defines the format of the file, contains the signature, image file header, and other information headers.

The PE file header is the section with the least human-readable output. You can identify the start of the PE file header from the PE stub in the hex dump section below:

```
000000E0  00 00 00 00 00 00 00 00 50 45 00 00 64 86 06 00  ........PE..d†..
...
```

### 4. Image Optional Header
Despite its name, the Image Optional Header is an important part of the PE File Header. It includes the Data Dictionaries, which point to the image data directory structure.

### 5. Section Table
The Section Table defines the available sections and information in the image. Sections store the contents of the file, such as code, imports, and data. You can identify each section definition from the table in the hex dump section below:

```
000001F0  2E 74 65 78 74 00 00 00 D0 0B 00 00 00 10 00 00  .text...Ð.......
...
```

## Sections and Their Purposes

| Section   | Purpose                                      |
|-----------|----------------------------------------------|
| `.text`   | Contains executable code and entry point     |
| `.data`   | Contains initialized data (strings, variables, etc.) |
| `.rdata` or `.idata` | Contains imports (Windows API) and DLLs |
| `.reloc`  | Contains relocation information              |
| `.rsrc`   | Contains application resources (images, etc.)|
| `.debug`  | Contains debug information                   |

Now that the headers have defined the format and function of the file, the sections define the contents and data of the file.
