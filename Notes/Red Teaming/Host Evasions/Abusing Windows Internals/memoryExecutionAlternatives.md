# Memory Execution Alternatives

Depending on the environment you are placed in, you may need to alter the way that you execute your shellcode. This could occur when there are hooks on an API call that you cannot evade or unhook, an EDR is monitoring threads, etc.

Up to this point, we have primarily looked at methods of allocating and writing data to and from local/remote processes. Execution is also a vital step in any injection technique, although not as important when attempting to minimize memory artifacts and IOCs (Indicators of Compromise). Unlike allocating and writing data, execution has many options to choose from.

Throughout this room, we have observed execution primarily through `CreateThread` and its counterpart, `CreateRemoteThread`.

In this task, we will cover three other execution methods that can be used depending on the circumstances of your environment.

---

## Invoking Function Pointers

The void function pointer is an oddly novel method of memory block execution that relies solely on typecasting. This technique can only be executed with locally allocated memory but does not rely on any API calls or other system functionality.

The one-liner below is the most common form of the void function pointer, but we can break it down further to explain its components:

```c
((void(*)())addressPointer)();
```

### Explanation:
1. **Create a function pointer**: `(void(*)())` (outlined in red).
2. **Cast the allocated memory pointer or shellcode array into the function pointer**: `(<function pointer>)addressPointer` (outlined in yellow).
3. **Invoke the function pointer to execute the shellcode**: `();` (outlined in green).

This technique has a very specific use case but can be very evasive and helpful when needed.

---

## Asynchronous Procedure Calls (APC)

From the Microsoft documentation on Asynchronous Procedure Calls:  
“An asynchronous procedure call (APC) is a function that executes asynchronously in the context of a particular thread.”

An APC function is queued to a thread through `QueueUserAPC`. Once queued, the APC function results in a software interrupt and executes the function the next time the thread is scheduled.

### Requirements:
For a userland/user-mode application to queue an APC function, the thread must be in an **alertable state**. An alertable state requires the thread to be waiting for a callback such as `WaitForSingleObject` or `Sleep`.

### Example Code:
```c
QueueUserAPC(
    (PAPCFUNC)addressPointer, // APC function pointer to allocated memory defined by winnt
    pinfo.hThread,            // Handle to thread from PROCESS_INFORMATION structure
    (ULONG_PTR)NULL
);
ResumeThread(
    pinfo.hThread // Handle to thread from PROCESS_INFORMATION structure
);
WaitForSingleObject(
    pinfo.hThread, // Handle to thread from PROCESS_INFORMATION structure
    INFINITE       // Wait infinitely until alerted
);
```

This technique is a great alternative to thread execution, but it has recently gained traction in detection engineering, and specific traps are being implemented for APC abuse. It can still be a great option depending on the detection measures you are facing.

---

## Section Manipulation

A commonly seen technique in malware research is PE (Portable Executable) and section manipulation. As a refresher, the PE format defines the structure and formatting of an executable file in Windows. For execution purposes, we are mainly focused on the sections, specifically `.data` and `.text`. Tables and pointers to sections are also commonly used to execute data.

We will not go in-depth with these techniques since they are complex and require a large technical breakdown, but we will discuss their basic principles.

### Steps:
1. Obtain a PE dump (commonly accomplished with a DLL or other malicious file fed into `xxd`).
2. Use math to move through the physical hex data, which is translated to PE data.

### Common Techniques:
- **RVA Entry Point Parsing**
- **Section Mapping**
- **Relocation Table Parsing**

With all injection techniques, the ability to mix and match commonly researched methods is endless. This provides you, as an attacker, with a plethora of options to manipulate your malicious data and execute it.

---