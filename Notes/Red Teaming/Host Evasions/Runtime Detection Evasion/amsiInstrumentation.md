# AMSI Instrumentation Overview

AMSI (Antimalware Scan Interface) instrumentation can be complex, involving multiple DLLs and varying execution strategies depending on where and how it is used. By design, AMSI serves as an interface for other anti-malware products, utilizing multiple provider DLLs and API calls based on the execution context and layer.

AMSI is instrumented from `System.Management.Automation.dll`, a .NET assembly developed by Microsoft. According to Microsoft documentation:

> "Assemblies form the fundamental units of deployment, version control, reuse, activation scoping, and security permissions for .NET-based applications."

The .NET assembly will instrument other DLLs and API calls depending on the interpreter (e.g., PowerShell, VBScript) and whether execution occurs on disk or in memory. The diagram below (not shown) illustrates how data is processed through the layers and which DLLs/API calls are involved.

As data flows through the model, various API calls and interfaces are instrumented at each layer, depending on the interpreter used. Understanding the complete AMSI model is important, but it can be broken down into core components, as shown in the referenced diagram.

> **Note:** AMSI is only instrumented when loaded from memory via the CLR. If executed from disk, it is assumed that `MsMpEng.exe` (Windows Defender) is already instrumented.

Most research and known bypasses target the Win32 API layer, specifically by manipulating the `AmsiScanBuffer` API call.

AMSI also exposes an "Other Applications" interface, allowing third-party AV providers to instrument AMSI from their products. Microsoft documents AMSI functions and the AMSI stream interface for this purpose.

## AMSI PowerShell Instrumentation

To better understand AMSI implementation and its detection mechanisms, we can examine the code responsible for AMSI instrumentation in PowerShell. Using [InsecurePowerShell](https://github.com/Cobbr/InsecurePowerShell)—a fork of PowerShell with security features removed—we can compare commits and observe security-related changes. AMSI is instrumented in just twelve lines of code within `src/System.Management.Automation/engine/runtime/CompiledScriptBlock.cs`:

```csharp
var scriptExtent = scriptBlockAst.Extent;
if (AmsiUtils.ScanContent(scriptExtent.Text, scriptExtent.File) == AmsiUtils.AmsiNativeMethods.AMSI_RESULT.AMSI_RESULT_DETECTED)
{
    var parseError = new ParseError(scriptExtent, "ScriptContainedMaliciousContent", ParserStrings.ScriptContainedMaliciousContent);
    throw new ParseException(new[] { parseError });
}

if (ScriptBlock.CheckSuspiciousContent(scriptBlockAst) != null)
{
    HasSuspiciousContent = true;
}
```

By understanding how AMSI is instrumented and leveraging research from the community, it is possible to develop and use bypasses that evade AMSI or its utilities.