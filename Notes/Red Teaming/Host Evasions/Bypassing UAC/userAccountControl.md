# User Account Control (UAC)

## What is UAC?

User Account Control (UAC) is a Windows security feature that ensures new processes run in the security context of a non-privileged account by default. This applies to all users, including administrators. The goal is to prevent unauthorized actions based solely on user identity.

**Example:**  
If a user in the Administrators group (e.g., BOB) downloads and runs a malicious application, UAC restricts the application's privileges. Without UAC, the application would inherit full administrator rights.

---

## UAC Elevation

When an administrator needs to perform a privileged task, UAC prompts the user to confirm elevation. This is done via a dialog box, allowing the application to run with administrative privileges.

---

## Integrity Levels

UAC uses Mandatory Integrity Control (MIC) to assign an Integrity Level (IL) to users, processes, and resources. Higher ILs can access resources with lower or equal ILs. MIC takes precedence over Windows DACLs.

**Windows Integrity Levels:**

| Integrity Level | Use                                                                                  |
|-----------------|-------------------------------------------------------------------------------------|
| Low             | Used for Internet-facing processes (e.g., Internet Explorer). Very limited access.   |
| Medium          | Default for standard users and administrators' filtered tokens.                      |
| High            | Used by administrators' elevated tokens (if UAC enabled).                            |
| System          | Reserved for system processes.                                                       |

Processes inherit the IL of the calling user's access token.

---

## Filtered Tokens

UAC handles users differently at logon:

- **Non-administrators:** Receive a single token (Medium IL).
- **Administrators:** Receive two tokens:
    - **Filtered Token:** Administrator privileges stripped (Medium IL).
    - **Elevated Token:** Full administrator privileges (High IL).

Administrators use the filtered token by default and must explicitly request elevation.

---

## Opening an Application

When opening a console:

- **As non-privileged user:** Process gets a Medium IL token.
- **As administrator:** Process gets a High IL token.

**Process Hacker** can show the differences:
- Filtered token (Medium IL): Few privileges.
- Elevated token (High IL): Many privileges.

Medium IL processes are denied administrator group privileges.

---

## UAC Settings

UAC can be configured at four notification levels:

1. **Always notify:** Prompt for all changes.
2. **Notify me only when programs try to make changes:** Prompt for program changes, not Windows settings.
3. **Notify me only when programs try to make changes (do not dim desktop):** Same as above, but no secure desktop.
4. **Never notify:** UAC prompt disabled; administrators always use a high privilege token.

**Default:** Notify me only when programs try to make changes.

From an attacker's perspective, only "Always notify" offers extra protection.

---

## UAC Internals

The **Application Information Service (Appinfo)** manages elevation:

1. User requests to run as administrator.
2. `ShellExecute` API call with `runas` verb.
3. Appinfo checks application manifest for AutoElevation.
4. Appinfo runs `consent.exe` to show the UAC prompt on a secure desktop.
5. If consent is given, Appinfo executes the request with the elevated token.

---

## Bypassing UAC

Attackers may obtain a shell on a Windows host with an administrative account but only have a Medium IL (filtered token). Attempting privileged actions (e.g., creating a user) fails:

```powershell
PS C:\Users\attacker> net user backdoor Backd00r /add
System error 5 has occurred.

Access is denied.
```

Checking group membership:

```powershell
PS C:\Users\attacker> whoami /groups
```

Shows "Group used for deny only" for administrator groups and "Medium Mandatory Level".

Even with an administrative user, UAC restricts administrative tasks unless using an elevated token.

**Note:**  
Microsoft does not consider UAC a security boundary, but rather a convenience. Bypassing UAC is not considered a vulnerability, so some bypass techniques remain unpatched.

Most bypasses leverage a High IL process to spawn an elevated process, inheriting the High IL without triggering the UAC prompt.

**Scenario:**  
You have access to a server with an administrative account via a Medium IL console. The goal is to obtain a High IL console without triggering UAC.
