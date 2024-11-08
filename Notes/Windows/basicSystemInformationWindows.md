## Basic System Information

Before issuing commands, we should note that we can only issue the commands within the Windows Path. You can issue the command set to check your path from the command line. The terminal output below shows the path where MS Windows will execute commands, as indicated by the line starting with Path=.

```shell
C:\>set
ALLUSERSPROFILE=C:\ProgramData
[...]
LOGNAME=strategos
NUMBER_OF_PROCESSORS=2
OS=Windows_NT
Path=C:\Windows\system32;C:\Windows;C:\Windows\System32\Wbem;C:\Windows\System32\WindowsPowerShell\v1.0\;C:\Windows\System32\OpenSSH\;C:\Windows\system32\config\systemprofile\AppData\Local\Microsoft\WindowsApps;C:\Users\strategos\AppData\Local\Microsoft\WindowsApps;
[...]
```

Let’s use the `ver` command to determine the operating system (OS) version. The terminal below shows an example output.

```shell
C:\>ver                                                                                                                                              
Microsoft Windows [Version 10.0.17763.1821]
```

Enough warming up. Let’s discover more in-depth information about the system. We can run the `systeminfo` command to list various information about the system such as OS information, system details, processor, and memory. The terminal below shows a snippet of the displayed output.

```shell
C:\>systeminfo

Host Name:                 WIN-SRV-2019
OS Name:                   Microsoft Windows Server 2019 Datacenter
OS Version:                10.0.17763 N/A Build 17763
OS Manufacturer:           Microsoft Corporation
OS Configuration:          Standalone Server
OS Build Type:             Multiprocessor Free
[...]
```

Before moving on, it is good to mention a couple of tricks.

First, you can pipe it through `more` if the output is too long. Then, you can view it page after page by pressing the space bar button. To demonstrate this, try running `driverquery` and compare it with running `driverquery | more`. In the latter, you can display the output page by page and you can exit it using `CTRL + C`.

- `help` - Provides help information for a specific command
- `cls` - Clears the Command Prompt screen.

## Network Configuration

Most of us are used to looking up MS Windows network configuration from the GUI interface. The command-line interface provides many networking-related commands to look up your current configuration, check ongoing connections, and troubleshoot networking issues.

### Checking Network Information

You can check your network information using `ipconfig`. The terminal output below shows our IP address, subnet mask, and default gateway.

```shell
C:\>ipconfig

Windows IP Configuration

Ethernet adapter Ethernet:

    Connection-specific DNS Suffix  . : eu-west-1.compute.internal
    Link-local IPv6 Address . . . . . : fe80::90df:4861:ba40:f2a8%4
    IPv4 Address. . . . . . . . . . . : 10.10.230.237
    Subnet Mask . . . . . . . . . . . : 255.255.0.0
    Default Gateway . . . . . . . . . : 10.10.0.1
```

You can also use `ipconfig /all` for more information about your network configuration. As shown in the terminal below, we can view our DNS servers and confirm that DHCP is enabled.

```shell
C:\>ipconfig /all

Ethernet adapter Ethernet 3:

    Connection-specific DNS Suffix  . : eu-west-1.compute.internal
    Description . . . . . . . . . . . : Amazon Elastic Network Adapter
    Physical Address. . . . . . . . . : 02-B7-DF-1D-0D-99
    DHCP Enabled. . . . . . . . . . . : Yes
    Autoconfiguration Enabled . . . . : Yes
    Link-local IPv6 Address . . . . . : fe80::90df:4861:ba40:f2a8%4(Preferred) 
    IPv4 Address. . . . . . . . . . . : 10.10.230.237(Preferred) 
    Subnet Mask . . . . . . . . . . . : 255.255.0.0
    Lease Obtained. . . . . . . . . . : Wednesday, May 1, 2024 2:38:05 PM
    Lease Expires . . . . . . . . . . : Wednesday, May 1, 2024 4:08:07 PM
    Default Gateway . . . . . . . . . : 10.10.0.1
    DHCP Server . . . . . . . . . . . : 10.10.0.1
    DHCPv6 IAID . . . . . . . . . . . : 134353458
    DHCPv6 Client DUID. . . . . . . . : 00-01-00-01-27-E3-D1-2B-0E-F8-30-D0-72-3F
    DNS Servers . . . . . . . . . . . : 10.0.0.2
    NetBIOS over Tcpip. . . . . . . . : Enabled
```

### Network Troubleshooting

One common troubleshooting task is checking if the server can access a particular server on the Internet. The command syntax is `ping target_name`. Inspired by ping-pong, we send a specific ICMP packet and listen for a response. If a response is received, we know that we can reach the target and that the target can reach us.

Let’s find out if we reach example.com. In the terminal output below, we can see that we have successfully received four replies. Furthermore, we got some statistics; for instance, the average round trip time is 78 milliseconds.

```shell
C:\>ping example.com

Pinging example.com [93.184.215.14] with 32 bytes of data:
Reply from 93.184.215.14: bytes=32 time=78ms TTL=52
Reply from 93.184.215.14: bytes=32 time=78ms TTL=52
Reply from 93.184.215.14: bytes=32 time=78ms TTL=52
Reply from 93.184.215.14: bytes=32 time=78ms TTL=52

Ping statistics for 93.184.215.14:
     Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
     Minimum = 78ms, Maximum = 78ms, Average = 78ms
```

Another valuable tool for troubleshooting is `tracert`, which stands for trace route. The command `tracert target_name` traces the network route traversed to reach the target. Without getting into more details, it expects the routers on the path to notify us if they drop a packet because its time-to-live (TTL) has reached zero. The terminal output below shows that we passed through 15 routers before reaching our target.

```shell
C:\>tracert example.com

Tracing route to example.com [93.184.215.14]
over a maximum of 30 hops:

  1    59 ms    32 ms    42 ms  ec2-3-248-240-3.eu-west-1.compute.amazonaws.com [3.248.240.3]
  2     *        *        *     Request timed out.
  3     *        *        *     Request timed out.
  4     *        *        *     Request timed out.
  5     *        *        *     Request timed out.
  6     *        *        *     Request timed out.
  7     *        *        *     Request timed out.
  8     *        *        *     Request timed out.
  9    <1 ms    13 ms    <1 ms  100.100.2.56
 10    15 ms    11 ms    11 ms  ae-42.a03.londen12.uk.bb.gin.ntt.net [131.103.117.104]
 11    17 ms    11 ms    12 ms  ae-14.r20.londen12.uk.bb.gin.ntt.net [129.250.3.248]
 12    81 ms    80 ms    80 ms  ae-7.r20.nwrknj03.us.bb.gin.ntt.net [129.250.6.147]
 13    83 ms    83 ms    86 ms  ae-0.a02.nycmny17.us.bb.gin.ntt.net [129.250.3.9]
 14    79 ms    79 ms    96 ms  ce-0-3-0.a02.nycmny17.us.ce.gin.ntt.net [128.241.1.14]
 15    81 ms    86 ms    79 ms  ae-67.core1.nyd.edgecastcdn.net [152.195.68.135]
 16    78 ms    78 ms    78 ms  93.184.215.14

Trace complete.
```

### More Networking Commands

One networking command worth knowing is `nslookup`. It looks up a host or domain and returns its IP address. The syntax `nslookup example.com` will look up example.com using the default name server; however, `nslookup example.com 1.1.1.1` will use the name server one.one.one.one. The terminal below shows the output of both commands. The results are identical; however, you can see that the answers were retrieved from different name servers.

```shell
C:\>nslookup example.com
Server:  ip-10-0-0-2.eu-west-1.compute.internal
Address:  10.0.0.2

Non-authoritative answer:
Name:    example.com
Addresses:  2606:2800:21f:cb07:6820:80da:af6b:8b2c
             93.184.215.14

C:>nslookup example.com 1.1.1.1
Server:  one.one.one.one
Address:  1.1.1.1

Non-authoritative answer:
Name:    example.com
Addresses:  2606:2800:21f:cb07:6820:80da:af6b:8b2c
             93.184.215.14
```

The final networking command we will cover in this room is `netstat`. This command displays current network connections and listening ports. A basic `netstat` command with no arguments will show you established connections, as shown below. In this case, we only have one SSH connection; we figured out it is SSH because it is bound to port 22.

```shell
C:\>netstat

Active Connections

  Proto  Local Address          Foreign Address        State
  TCP    10.10.230.237:22       ip-10-11-81-126:53486  ESTABLISHED
```

If you are curious about the other options, you can run `netstat -h`, where `-h` displays the help page. We opted for the following options:

- `-a` displays all established connections and listening ports
- `-b` shows the program associated with each listening port and established connection
- `-o` reveals the process ID (PID) associated with the connection
- `-n` uses a numerical form for addresses and port numbers

We combine these four options and execute the `netstat -abon` command. The result is quite long, but we display the first few lines in the terminal below. It is clear now that the executable `sshd.exe` is responsible for listening for incoming connections on port 22, as shown in the first line. We can also see the process ID (PID) associated with each connection.

```shell
C:\>netstat -abon

Active Connections

  Proto  Local Address          Foreign Address        State           PID 
  TCP    0.0.0.0:22             0.0.0.0:0              LISTENING       2116
 [sshd.exe]
  TCP    0.0.0.0:135            0.0.0.0:0              LISTENING       820
  RpcSs 
 [svchost.exe]
[...]
  TCP    0.0.0.0:49669          0.0.0.0:0              LISTENING       2036
 [spoolsv.exe]
  TCP    0.0.0.0:49670          0.0.0.0:0              LISTENING       584 
 Can not obtain ownership information
  TCP    0.0.0.0:49686          0.0.0.0:0              LISTENING       592
 [lsass.exe]
  TCP    10.10.230.237:22       10.11.81.126:53486     ESTABLISHED     2116 
 [sshd.exe]
 [...]
```

## Working With Directories

### Check Current Directory

- **Command:** `cd`
- **Output:** Displays the current drive and directory.

### View Child Directories

- **Command:** `dir`
- **Output:** Displays a list of files and directories in the current directory.

**Options:**

- `dir /a`: Displays hidden and system files.
- `dir /s`: Displays files in the current directory and all subdirectories.

### Visualize Directory Tree

- **Command:** `tree`
- **Output:** Displays a graphical representation of all directories and subdirectories.

### Change Directories

- **Command:** `cd directory_name`
  - Move into a directory.
- **Command:** `cd ..`
  - Move up one level in the directory structure.

### Create Directory

- **Command:** `mkdir directory_name`
- **Output:** Creates a new directory.

### Delete Directory

- **Command:** `rmdir directory_name`
- **Output:** Removes an empty directory.

## Working With Files

### View File Content

- **Command:** `type filename.txt`
- **Output:** Displays the content of a text file.

- **Command:** `more filename.txt`
- **Output:** Displays the content of a text file page by page.

### Copy a File

- **Command:** `copy source_file destination_file`
- **Example:**

  ```bash
  copy test.txt test2.txt
  ```

- **Output:** Copies the file `test.txt` to `test2.txt`.

### Move a File

- **Command:** `move source_file destination_directory`
- **Example:**

  ```bash
  move test2.txt ..
  ```

- **Output:** Moves the file `test2.txt` to the parent directory.

### Delete a File

- **Command:** `del filename.txt` or `erase filename.txt`
- **Output:** Deletes a file.

### Use Wildcards

- **Example:**

  ```bash
  copy *.md C:\Markdown
  ```

- **Output:** Copies all `.md` files to the directory `C:\Markdown`.


------

Managing Processes from the Command Line
List Running Processes
Command: tasklist
Output: Lists all running processes with details such as Image Name, PID, Session Name, and Memory Usage.

Filter Processes
Command: tasklist /FI "imagename eq process_name.exe"
Output: Lists processes that match the specified filter.
Example:

bash
Copier le code
tasklist /FI "imagename eq sshd.exe"
Output: Shows all processes named sshd.exe.

Terminate a Process
Command: taskkill /PID process_id
Example:

bash
Copier le code
taskkill /PID 4567
Output: Terminates the process with PID 4567.

Help for More Filters
Command: tasklist /?
Output: Displays all available filters for the tasklist command.


-----
