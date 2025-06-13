# Firewall Evasion Using Port Hopping

Three common firewall evasion techniques are:

1. **Port hopping**
2. **Port tunneling**
3. **Use of non-standard ports**

---

## Port Hopping

Port hopping is a technique where an application switches from one port to another until it can establish and maintain a connection. In other words, the application tries different ports until it successfully connects. Some legitimate applications use this technique to evade firewalls.

There is another type of port hopping where the application establishes a connection on one port, transmits some data, then establishes a new connection on a different port and continues sending data. This makes it more difficult for defenders to detect and track all exchanged traffic.

---

## Practical Example

On the AttackBox, you can use the following command to listen on a specific TCP port:

```bash
ncat -lvnp PORT_NUMBER
```

- `-l`: Listen for incoming connections
- `-v`: Verbose output (optional)
- `-n`: Do not resolve hostnames via DNS (optional)
- `-p`: Specify the port number

For example, to listen on TCP port 1025:

```bash
ncat -lvnp 1025
```

**Sample Output:**
```
pentester@TryHackMe$ ncat -lvnp 1025
Ncat: Version 7.91 ( https://nmap.org/ncat )
Ncat: Listening on :::1025
Ncat: Listening on 0.0.0.0:1025
```

---

## Testing Connectivity

To test if the target machine can connect to the AttackBox on TCP port 1025:

1. Browse to `http://MACHINE_IP:8080` to access a web page that allows command execution on the target machine.
2. Use Netcat to connect to the AttackBox:

    ```bash
    ncat ATTACKBOX_IP 1025
    ```

If the firewall allows the connection, you will see a notification on the AttackBox terminal:

```
pentester@TryHackMe$ ncat -lvnp 1025
Ncat: Version 7.91 ( https://nmap.org/ncat )
Ncat: Listening on :::1025
Ncat: Listening on 0.0.0.0:1025
Ncat: Connection from 10.10.30.130.
Ncat: Connection from 10.10.30.130:51292.
```

---

> **Note:** If the port number is less than 1024, you need to run `ncat` as root.