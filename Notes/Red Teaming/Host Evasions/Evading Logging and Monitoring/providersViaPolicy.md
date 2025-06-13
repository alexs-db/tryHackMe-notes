# ETW Logging and PowerShell Providers via Policy

Event Tracing for Windows (ETW) provides extensive logging coverage by default, but some features are disabled unless explicitly enabled due to the volume of logs they generate. These features can be managed through Group Policy Object (GPO) settings. Two of the most popular GPO providers offer coverage over PowerShell, including **script block logging** and **module logging**.

---

## Script Block Logging

Script block logging records any script blocks executed within a PowerShell session. Introduced in PowerShell v4 and improved in v5, the ETW provider reports two main event IDs:

| Event ID | Purpose                    |
|----------|----------------------------|
| 4103     | Logs command invocation    |
| 4104     | Logs script block execution|

**Event ID 4104** is particularly relevant to attackers, as it can expose malicious scripts if not properly obfuscated.  
Example of a 4104 log:

```
Event ID: 4104
Source: Microsoft-Windows-PowerShell
Category: Execute a Remote Command
Log: Microsoft-Windows-PowerShell/Operational
Message: Creating Scriptblock text (1 of 1):
Write-Host PowerShellV5ScriptBlockLogging

ScriptBlock ID: 6d90e0bb-e381-4834-8fe2-5e076ad267b3
Path:
```

---

## Module Logging

Module logging is a verbose provider that logs any modules and data sent from them. Introduced in PowerShell v3, each module acts as a provider and logs its own activity. Events are written to **event ID 4103**.  
Example of a 4103 log:

```
Event ID: 4103
Source: Microsoft-Windows-PowerShell
Category: Executing Pipeline
Log: Microsoft-Windows-PowerShell/Operational

Message: CommandInvocation(Write-Host): "Write-Host"
ParameterBinding(Write-Host): name="Object"; value="TestPowerShellV5"

Context:
Severity = Informational
Host Name = ConsoleHost
...
[snip]
...
User = DOMAIN\username
Connected User =
Shell ID = Microsoft.PowerShell
```

**Event ID 4103** is less targeted by attackers due to the high volume of logs, which can lead to it being deprioritized or disabled.

---

## Evasion Considerations

While attackers may use ETW patches, these are not always practical or stealthy. Alternatively, targeting specific providers can gradually reduce visibility without drawing attention.

> **Goal:** Disable or limit specific providers to reduce monitoring of required components, while maintaining the appearance of an untampered environment.
