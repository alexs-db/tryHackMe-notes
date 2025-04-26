# Interacting with Windows Internals

Interacting with Windows internals may seem daunting, but it has been dramatically simplified. The most accessible and researched option to interact with Windows internals is through Windows API calls. The Windows API provides native functionality to interact with the Windows operating system. It includes the Win32 API and, less commonly, the Win64 API.

We will provide a brief overview of a few specific API calls relevant to Windows internals. For more information, check out the Windows API room.

## Overview of Windows Internals

Most Windows internals components require interaction with physical hardware and memory. The Windows kernel controls all programs and processes, bridging software and hardware interactions. This is crucial since many Windows internals require memory interaction.

By default, an application cannot directly interact with the kernel or modify physical hardware. This limitation is addressed through processor modes and access levels.

### Processor Modes

A Windows processor operates in two modes: **user mode** and **kernel mode**. The processor switches between these modes depending on access and the requested operation.

| User Mode                     | Kernel Mode                     |
|-------------------------------|----------------------------------|
| No direct hardware access     | Direct hardware access          |
| Creates a process in a private virtual address space | Runs in a single shared virtual address space |
| Access to "owned memory locations" | Access to entire physical memory |

Applications started in user mode (or "userland") remain in that mode until a system call is made or an API is used. When a system call is made, the application switches to kernel mode. This transition is often referred to as the **"Switching Point"**.

### Language Interaction with the Win32 API

When programming languages interact with the Win32 API, the process can become more complex. For example, in C#, the application goes through the CLR (Common Language Runtime) before interacting with the Win32 API and making system calls.

## Demonstrating Interaction with Memory

To demonstrate a proof-of-concept for interacting with memory, we will inject a message box into a local process. The steps are as follows:

1. Allocate local process memory for the message box.
2. Write/copy the message box to the allocated memory.
3. Execute the message box from local process memory.

### Step 1: Allocate Process Memory

Use `OpenProcess` to obtain the handle of the specified process:

```c
HANDLE hProcess = OpenProcess(
    PROCESS_ALL_ACCESS, // Defines access rights
    FALSE,              // Target handle will not be inherited
    DWORD(atoi(argv[1])) // Local process supplied by command-line arguments
);
```

### Step 2: Allocate Memory Region

Use `VirtualAllocEx` to allocate a region of memory for the payload buffer:

```c
remoteBuffer = VirtualAllocEx(
    hProcess,            // Opened target process
    NULL, 
    sizeof payload,      // Region size of memory allocation
    (MEM_RESERVE | MEM_COMMIT), // Reserves and commits pages
    PAGE_EXECUTE_READWRITE // Enables execution and read/write access to the committed pages
);
```

### Step 3: Write Payload to Memory

Use `WriteProcessMemory` to write the payload to the allocated memory region:

```c
WriteProcessMemory(
    hProcess,        // Opened target process
    remoteBuffer,    // Allocated memory region
    payload,         // Data to write
    sizeof payload,  // Byte size of data
    NULL
);
```

### Step 4: Execute Payload

Use `CreateRemoteThread` to execute the payload from memory:

```c
remoteThread = CreateRemoteThread(
    hProcess,                      // Opened target process
    NULL, 
    0,                             // Default size of the stack
    (LPTHREAD_START_ROUTINE)remoteBuffer, // Pointer to the starting address of the thread
    NULL, 
    0,                             // Ran immediately after creation
    NULL
);
```

This process demonstrates how to interact with Windows internals using API calls.