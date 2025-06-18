# Credential Injection in Active Directory

Before jumping into AD objects and enumeration, let's first talk about credential injection methods. Often, credentials are found without compromising a domain-joined machine. Some enumeration techniques require a specific setup to work.

---

## Windows vs Linux

> "If you know the enemy and know yourself, you need not fear the results of a hundred battles. If you know yourself but not the enemy, for every victory gained you will also suffer defeat."  
> — Sun Tzu, *Art of War*

You can get far with AD enumeration from a Kali machine. However, for in-depth enumeration and exploitation, you need to understand and mimic your target—meaning you need a Windows machine. This allows you to use built-in methods for enumeration and exploitation. One such tool is `runas.exe`.

---

## Runas Explained

Have you ever found AD credentials but nowhere to log in with them? `runas` may be the answer!

During security assessments, you may have network access and AD credentials but no means or privileges to create a new domain-joined machine. You need to use those credentials on a Windows machine you control.

If you have AD credentials in the format `<username>:<password>`, you can use `runas` to inject the credentials into memory:

```sh
runas.exe /netonly /user:<domain>\<username> cmd.exe
```

**Parameters:**

- `/netonly`: Loads credentials for network authentication only. Local commands run as your standard account, but network connections use the specified account.
- `/user`: Provide the domain and username. Use the Fully Qualified Domain Name (FQDN) for better resolution.
- `cmd.exe`: The program to execute after credentials are injected. You can change this, but `cmd.exe` is safest.

When you run this command, you'll be prompted for a password. With `/netonly`, credentials aren't verified by a domain controller, so any password is accepted. You still need to confirm that the network credentials are loaded successfully.

> **Note:** If using your own Windows machine, run your first Command Prompt as Administrator. This injects an Administrator token into CMD, ensuring local commands run with administrative privileges. This does **not** grant network admin rights.

---

## It's Always DNS

> **Note:** These steps are only needed if using your own Windows machine, but are useful knowledge for red team exercises.

After entering the password, a new command prompt opens. To verify your credentials, list `SYSVOL`. Any AD account can read `SYSVOL`.

**What is SYSVOL?**  
A shared folder on all domain controllers storing Group Policy Objects (GPOs) and scripts. It's essential for AD as it delivers GPOs to all domain computers.

Before listing `SYSVOL`, configure your DNS. Sometimes internal DNS is set via DHCP or VPN, but not always. The safest DNS server is usually a domain controller. Using its IP, run in PowerShell:

```powershell
$dnsip = "<DC IP>"
$index = Get-NetAdapter -Name 'Ethernet' | Select-Object -ExpandProperty 'ifIndex'
Set-DnsClientServerAddress -InterfaceIndex $index -ServerAddresses $dnsip
```

Replace `'Ethernet'` with your network interface name.

Verify DNS with:

```sh
nslookup za.tryhackme.com
```

If it resolves to the DC IP, DNS is working. Now, test your credentials by listing `SYSVOL`:

```sh
dir \\za.tryhackme.com\SYSVOL\
```

Example output:

```
 Volume in drive \\za.tryhackme.com\SYSVOL is Windows
 Volume Serial Number is 1634-22A9

 Directory of \\za.tryhackme.com\SYSVOL

02/24/2022  09:57 PM    <DIR>          .
02/24/2022  09:57 PM    <DIR>          ..
02/24/2022  09:57 PM    <JUNCTION>     za.tryhackme.com [C:\Windows\SYSVOL\domain]
               0 File(s)              0 bytes
               3 Dir(s)  51,835,408,384 bytes free
```

It's also good to enumerate `SYSVOL` contents, as additional AD credentials may be present.

---

## IP vs Hostnames

**Question:** Is there a difference between `dir \\za.tryhackme.com\SYSVOL` and `dir \\<DC IP>\SYSVOL`? Why the fuss about DNS?

**Answer:**  
Yes, there's a difference due to authentication methods. Using the hostname, network authentication tries Kerberos first. Kerberos uses hostnames in tickets, so using the IP forces NTLM authentication. This can help you remain stealthy during red team assessments, as some organizations monitor for OverPass- and Pass-The-Hash attacks. Forcing NTLM is a useful trick to avoid detection.

---

## Using Injected Credentials

With `/netonly`, all network communication from that command prompt uses the injected credentials for authentication. This includes all applications started from that window.

For example, if an MS SQL database uses Windows Authentication and you're not domain-joined, start MS SQL Studio from that prompt. Even if it shows your local username, clicking "Log In" uses the injected AD credentials. You can also authenticate to web applications using NTLM Authentication.

We'll use this in the next task for our first AD enumeration technique.