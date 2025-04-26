# Dynamic Link Libraries (DLLs)

The Microsoft documentation describes a DLL as:

> "A library that contains code and data that can be used by more than one program at the same time."

DLLs are a core functionality behind application execution in Windows. According to the Windows documentation:

> "The use of DLLs helps promote modularization of code, code reuse, efficient memory usage, and reduced disk space. So, the operating system and the programs load faster, run faster, and take less disk space on the computer."

When a DLL is loaded as a function in a program, it becomes a dependency. Since a program depends on a DLL, attackers can target DLLs to control some aspect of execution or functionality.

## Common DLL Attack Techniques

- **DLL Hijacking (T1574.001)**
- **DLL Side-Loading (T1574.002)**
- **DLL Injection (T1055.001)**

## Creating a DLL

DLLs are created similarly to other projects or applications, with slight syntax modifications. Below is an example of a DLL created using the Visual C++ Win32 Dynamic-Link Library project:

### DLL Source Code

```cpp
#include "stdafx.h"
#define EXPORTING_DLL
#include "sampleDLL.h"

BOOL APIENTRY DllMain(HANDLE hModule, DWORD ul_reason_for_call, LPVOID lpReserved)
{
    return TRUE;
}

void HelloWorld()
{
    MessageBox(NULL, TEXT("Hello World"), TEXT("In a DLL"), MB_OK);
}
```

### DLL Header File

The header file defines the functions that are imported and exported. Its importance (or lack thereof) will be discussed in the next section.

```cpp
#ifndef INDLL_H
#define INDLL_H

#ifdef EXPORTING_DLL
    extern __declspec(dllexport) void HelloWorld();
#else
    extern __declspec(dllimport) void HelloWorld();
#endif

#endif
```

## Using DLLs in Applications

DLLs can be loaded into a program using either **load-time dynamic linking** or **run-time dynamic linking**.

### Load-Time Dynamic Linking

In load-time dynamic linking, explicit calls to the DLL functions are made from the application. This requires a header (`.h`) and an import library (`.lib`) file. Below is an example:

```cpp
#include "stdafx.h"
#include "sampleDLL.h"

int APIENTRY WinMain(HINSTANCE hInstance, HINSTANCE hPrevInstance, LPSTR lpCmdLine, int nCmdShow)
{
    HelloWorld();
    return 0;
}
```

### Run-Time Dynamic Linking

In run-time dynamic linking, the DLL is loaded at runtime using functions like `LoadLibrary` or `LoadLibraryEx`. After loading, `GetProcAddress` is used to identify the exported DLL function to call. Below is an example:

```cpp
typedef VOID (*DLLPROC)(LPTSTR);

HINSTANCE hinstDLL;
DLLPROC HelloWorld;
BOOL fFreeDLL;

hinstDLL = LoadLibrary("sampleDLL.dll");
if (hinstDLL != NULL)
{
    HelloWorld = (DLLPROC)GetProcAddress(hinstDLL, "HelloWorld");
    if (HelloWorld != NULL)
        (HelloWorld)();
    fFreeDLL = FreeLibrary(hinstDLL);
}
```

### Malicious Use of Run-Time Dynamic Linking

Threat actors often prefer run-time dynamic linking over load-time dynamic linking. This is because transferring a single DLL between memory regions is more manageable than importing files with additional requirements.