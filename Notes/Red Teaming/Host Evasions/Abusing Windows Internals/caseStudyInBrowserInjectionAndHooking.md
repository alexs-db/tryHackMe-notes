# Case Study: Browser Injection and Hooking

## Overview

To understand the implications of process injection, we can observe the TTPs (Tactics, Techniques, and Procedures) of TrickBot.

**Credit for initial research:** SentinelLabs

TrickBot is a well-known banking malware that has recently regained popularity in financial crimeware. The main function of the malware we will be observing is **browser hooking**. Browser hooking allows the malware to hook interesting API calls that can be used to intercept or steal credentials.

---

## Targeting Browsers

From SentinelLabs' reverse engineering, it is clear that `OpenProcess` is being used to obtain handles for common browser paths, as seen in the disassembly below:

```assembly
push   eax
push   0
push   438h
call   ds:OpenProcess
mov    edi, eax
mov    [edp,hProcess], edi
test   edi, edi
jz     loc_100045EE

push   offset Srch            ; "chrome.exe"
lea    eax, [ebp+pe.szExeFile]
...
mov    eax, ecx
push   offset aIexplore_exe   ; "iexplore.exe"
push   eax                    ; lpFirst
...
mov    eax, ecx
push   offset aFirefox_exe    ; "firefox.exe"
push   eax                    ; lpFirst
...
mov    eax, ecx
push   offset aMicrosoftedgec ; "microsoftedgecp.exe"
...
```

---

## Reflective Injection Program Flow

The current source code for the reflective injection is unclear, but SentinelLabs has outlined the basic program flow of the injection:

1. Open Target Process (`OpenProcess`)
2. Allocate memory (`VirtualAllocEx`)
3. Copy function into allocated memory (`WriteProcessMemory`)
4. Copy shellcode into allocated memory (`WriteProcessMemory`)
5. Flush cache to commit changes (`FlushInstructionCache`)
6. Create a remote thread (`RemoteThread`)
7. Resume the thread or fallback to create a new user thread (`ResumeThread` or `RtlCreateUserThread`)

---

## Hook Installer Function

Once injected, TrickBot will call its hook installer function copied into memory at step three. SentinelLabs has provided the following pseudo-code for the installer function:

```c
relative_offset = myHook_function - *(_DWORD *)(original_function + 1) - 5;
v8 = (unsigned __int8)original_function[5];
trampoline_lpvoid = *(void **)(original_function + 1);
jmp_32_bit_relative_offset_opcode = 0xE9u; // "0xE9" -> opcode for a jump with a 32-bit relative offset

if (VirtualProtectEx((HANDLE)0xFFFFFFFF, trampoline_lpvoid, v8, 0x40u, &flOldProtect)) {
    v10 = *(_DWORD *)(original_function + 1);
    v11 = (unsigned __int8)original_function[5] - (_DWORD)original_function - 0x47;
    original_function[66] = 0xE9u;
    *(_DWORD *)(original_function + 0x43) = v10 + v11;
    write_hook_iter(v10, &jmp_32_bit_relative_offset_opcode, 5); // Manually write the hook
    VirtualProtectEx( // Return to original protect state
        (HANDLE)0xFFFFFFFF,
        *(LPVOID *)(original_function + 1),
        (unsigned __int8)original_function[5],
        flOldProtect,
        &flOldProtect
    );
}
result = 1;
```

---

### Code Breakdown

This code may seem daunting at first, but it can be broken down into smaller sections:

1. **Function Pointers**  
   The first section of interesting code defines function pointers:
   ```c
   relative_offset = myHook_function - *(_DWORD *)(original_function + 1) - 5;
   v8 = (unsigned __int8)original_function[5];
   trampoline_lpvoid = *(void **)(original_function + 1);
   ```

2. **Memory Protection Modification**  
   The malware modifies the memory protections of the function using `VirtualProtectEx`:
   ```c
   if (VirtualProtectEx((HANDLE)0xFFFFFFFF, trampoline_lpvoid, v8, 0x40u, &flOldProtect))
   ```

3. **Hook Writing**  
   The code rewrites a hook to point to an opcode jump:
   ```c
   v10 = *(_DWORD *)(original_function + 1);
   v11 = (unsigned __int8)original_function[5] - (_DWORD)original_function - 0x47;
   original_function[66] = 0xE9u;
   *(_DWORD *)(original_function + 0x43) = v10 + v11;
   write_hook_iter(v10, &jmp_32_bit_relative_offset_opcode, 5);
   ```

4. **Restoring Memory Protections**  
   Finally, it restores the function to its original memory protections:
   ```c
   VirtualProtectEx(
       (HANDLE)0xFFFFFFFF,
       *(LPVOID *)(original_function + 1),
       (unsigned __int8)original_function[5],
       flOldProtect,
       &flOldProtect
   );
   ```

---

## Key Takeaway

The main takeaway of the hooking function for TrickBot is that it injects itself into browser processes using reflective injection and hooks API calls from the injected function.