# Harvesting Passwords from Usual Spots

The easiest way to gain access to another user is to gather credentials from a compromised machine. Such credentials could exist for many reasons, including a careless user leaving them around in plaintext files or being stored by software like browsers or email clients.

This task will present some known places to look for passwords on a Windows system.

Before proceeding, remember to click the **Start Machine** button. You will be using the same machine throughout tasks 3 to 5. If you are using the AttackBox, this is also a good moment to start it as you'll need it for the following tasks.

In case you prefer connecting to the target machine via RDP, you can use the following credentials:

- **User**: `thm-unpriv`
- **Password**: `Password321`

---

## Unattended Windows Installations

When installing Windows on a large number of hosts, administrators may use Windows Deployment Services, which allows for a single operating system image to be deployed to several hosts through the network. These kinds of installations are referred to as unattended installations as they don't require user interaction. Such installations require the use of an administrator account to perform the initial setup, which might end up being stored in the machine in the following locations:

- `C:\Unattend.xml`
- `C:\Windows\Panther\Unattend.xml`
- `C:\Windows\Panther\Unattend\Unattend.xml`
- `C:\Windows\system32\sysprep.inf`
- `C:\Windows\system32\sysprep\sysprep.xml`

As part of these files, you might encounter credentials:

```xml
<Credentials>
    <Username>Administrator</Username>
    <Domain>thm.local</Domain>
    <Password>MyPassword123</Password>
</Credentials>
```

---

## PowerShell History

Whenever a user runs a command using PowerShell, it gets stored in a file that keeps a memory of past commands. If a user runs a command that includes a password directly as part of the PowerShell command line, it can later be retrieved using the following command from a `cmd.exe` prompt:

```cmd
type %userprofile%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
```

> **Note**: The command above will only work from `cmd.exe`, as PowerShell won't recognize `%userprofile%` as an environment variable. To read the file from PowerShell, replace `%userprofile%` with `$Env:userprofile`.

---

## Saved Windows Credentials

Windows allows users to save credentials for other accounts. The following command lists saved credentials:

```cmd
cmdkey /list
```

While you can't see the actual passwords, if you notice any credentials worth trying, you can use them with the `runas` command and the `/savecred` option, as shown below:

```cmd
runas /savecred /user:admin cmd.exe
```

---

## IIS Configuration

Internet Information Services (IIS) is the default web server on Windows installations. The configuration of websites on IIS is stored in a file called `web.config`, which can store passwords for databases or configured authentication mechanisms. Depending on the installed version of IIS, you can find `web.config` in one of the following locations:

- `C:\inetpub\wwwroot\web.config`
- `C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\web.config`

To quickly find database connection strings in the file, use the following command:

```cmd
type C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\web.config | findstr connectionString
```

---

## Retrieve Credentials from Software: PuTTY

PuTTY is an SSH client commonly found on Windows systems. Instead of specifying a connection's parameters every time, users can store sessions where the IP, user, and other configurations are saved for later use. While PuTTY won't allow users to store their SSH password, it will store proxy configurations that include cleartext authentication credentials.

To retrieve the stored proxy credentials, search under the following registry key for `ProxyPassword` using this command:

```cmd
reg query HKEY_CURRENT_USER\Software\SimonTatham\PuTTY\Sessions\ /f "Proxy" /s
```

> **Note**: Simon Tatham is the creator of PuTTY (and his name is part of the path), not the username for which we are retrieving the password. The stored proxy username should also be visible after running the command above.

Just as PuTTY stores credentials, any software that stores passwords—such as browsers, email clients, FTP clients, SSH clients, VNC software, and others—will have methods to recover any saved passwords.