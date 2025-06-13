# Port Tunneling (Port Forwarding / Port Mapping)

Port tunneling, also known as port forwarding or port mapping, is a technique that forwards packets sent to one destination port to another destination port. For example, packets sent to port 80 on one system can be forwarded to port 8080 on another system.

## Port Tunneling Using `ncat`

Imagine you have a server behind a firewall that you cannot access directly from the outside. However, you discover that the firewall does not block traffic on certain ports. You can use this to your advantage by tunneling traffic through an allowed port.

### Example Scenario

Suppose there is an SMTP server listening on port 25, but the firewall blocks incoming connections to port 25 from the Internet. You notice that port 443 is not blocked. To bypass the firewall, you can send your packets to port 443, and after they pass through the firewall, forward them to port 25.

If you have the ability to run commands on a system behind the firewall, you can set up port forwarding with the following command:

```bash
ncat -lvnp 443 -c "ncat TARGET_SERVER 25"
```

**Explanation of the command:**

- `-l` : Listen mode.
- `-v` : Verbose output.
- `-n` : Numeric-only IP addresses (no DNS).
- `-p 443` : Listen on TCP port 443 (requires root privileges for ports < 1024).
- `-c` or `--sh-exec` : Executes the given command via `/bin/sh`.
- `"ncat TARGET_SERVER 25"` : Connects to the target server on port 25.

As a result, `ncat` listens on port 443 and forwards all incoming packets to port 25 on the target server. Since the firewall blocks port 25 but allows port 443, port tunneling is an effective way to evade firewall restrictions.
