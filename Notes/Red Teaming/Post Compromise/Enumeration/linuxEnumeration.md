# Post-Compromise Enumeration: Linux

This task focuses on enumerating a Linux machine after accessing a shell, such as bash. Although some commands provide information on more than one area, we grouped the commands into four categories depending on the information we expect to acquire:

- **System**
- **Users**
- **Networking**
- **Running Services**

We recommend that you click "Start AttackBox" and "Start Machine" so that you can experiment and answer the questions at the end of this task.

---

## System

### Linux Distribution and Version
To get information about the Linux distribution and release version, search for files or links ending with `-release` in `/etc/`:

```bash
ls /etc/*-release
```

#### Example: CentOS
```bash
$ ls /etc/*-release
/etc/centos-release  /etc/os-release  /etc/redhat-release  /etc/system-release
$ cat /etc/os-release
NAME="CentOS Linux"
VERSION="7 (Core)"
```

#### Example: Fedora
```bash
$ ls /etc/*-release
/etc/fedora-release@  /etc/os-release@  /etc/redhat-release@  /etc/system-release@
$ cat /etc/os-release
NAME="Fedora Linux"
VERSION="36 (Workstation Edition)"
```

### Hostname
To find the system’s name:
```bash
hostname
```

#### Example:
```bash
$ hostname
rpm-red-enum.thm
```

### Key Files
- `/etc/passwd`: Contains user account information (readable by all users).
- `/etc/group`: Contains group information (readable by all users).
- `/etc/shadow`: Contains hashed passwords (requires root privileges).

#### Example:
```bash
$ cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
michael:x:1001:1001::/home/michael:/bin/bash
```

```bash
$ sudo cat /etc/shadow
root:$6$pZlRFi09$qqgNBS.00qtcUF9x0yHetjJbXsw0PAwQabpCilmAB47ye3OzmmJVfV6DxBYyUoWBHtTXPU0kQEVUQfPtZPO3C.:19131:0:99999:7:::
```

### Installed Applications
- **RPM-based systems**: `rpm -qa`
- **Debian-based systems**: `dpkg -l`

#### Example:
```bash
$ dpkg -l
ii  accountsservice  0.6.55-0ubuntu12~20.04.5  amd64  query and manipulate user account information
```

---

## Users

### Logged-in Users
- `who`: Shows logged-in users.
- `whoami`: Prints the current user.
- `w`: Shows logged-in users and their activities.

#### Example:
```bash
$ who
root     tty1         2022-05-18 13:24
jane     pts/0        2022-05-19 07:17 (10.20.30.105)
```

### User IDs
- `id`: Prints real and effective user and group IDs.

#### Example:
```bash
$ id
uid=1003(jane) gid=1003(jane) groups=1003(jane)
```

### Recent Logins
- `last`: Displays the last logged-in users.

#### Example:
```bash
$ last
jane     pts/0        10.20.30.105     Thu May 19 07:17   still logged in
```

### Sudo Privileges
- `sudo -l`: Lists allowed commands for the invoking user.

---

## Networking

### IP Addresses
- `ip a s`: Displays IP addresses.
- `ifconfig -a`: Older command (may require installation).

#### Example:
```bash
$ ip a s
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500
    inet 10.20.30.129/24 brd 10.20.30.255 scope global
```

### DNS Servers
- `/etc/resolv.conf`: Contains DNS server information.

#### Example:
```bash
$ cat /etc/resolv.conf
nameserver 10.20.30.2
```

### Network Connections
- `netstat`: Displays network connections, routing tables, and interface statistics.
  - `netstat -plt`: Programs listening on TCP sockets.
  - `netstat -atupn`: All TCP/UDP connections in numeric format.

#### Example:
```bash
$ sudo netstat -plt
tcp        0      0 0.0.0.0:ssh             0.0.0.0:*               LISTEN      978/sshd
```

### Open Files
- `lsof -i`: Lists open Internet and network connections.

#### Example:
```bash
$ sudo lsof -i
COMMAND   PID      USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
sshd      978      root    3u  IPv4  20035      0t0  TCP *:ssh (LISTEN)
```

---

## Running Services

### Processes
- `ps`: Displays running processes.
  - `ps -e`: All processes.
  - `ps aux`: User-oriented format.
  - `ps axjf`: Process tree.

#### Example:
```bash
$ ps axf
   PID TTY      STAT   TIME COMMAND
   978 ?        Ss     0:00 /usr/sbin/sshd -D
  5665 ?        Ss     0:00  \_ sshd: peter [priv]
```

### Filtering Processes
- Use `grep` to filter process output.

#### Example:
```bash
$ ps -ef | grep peter
root       5665    978  0 07:11 ?        00:00:00 sshd: peter [priv]
```

This guide provides a comprehensive overview of post-compromise enumeration on Linux systems. Experiment with the commands to gain hands-on experience.