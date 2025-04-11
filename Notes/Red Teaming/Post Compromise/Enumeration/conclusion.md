# Post Compromise Enumeration - Conclusion

The focus of this room was on built-in command-line tools in both Linux and MS Windows systems. Many commands exist in both systems, although the command arguments and resulting output are different. The following tables show the primary Linux and MS Windows commands that we relied on to get more information about the system.

## Linux Commands

| Command              | Description                                   |
|----------------------|-----------------------------------------------|
| `hostname`           | Shows the system’s hostname                  |
| `who`                | Shows who is logged in                       |
| `whoami`             | Shows the effective username                 |
| `w`                  | Shows who is logged in and what they are doing |
| `last`               | Shows a listing of the last logged-in users  |
| `ip address show`    | Shows the network interfaces and addresses    |
| `arp`                | Shows the ARP cache                          |
| `netstat`            | Prints network connections                   |
| `ps`                 | Shows a snapshot of the current processes    |

## Windows Commands

| Command              | Description                                   |
|----------------------|-----------------------------------------------|
| `systeminfo`         | Shows OS configuration information, including service pack levels |
| `whoami`             | Shows the user name and group information along with the respective security identifiers |
| `netstat`            | Shows protocol statistics and current TCP/IP network connections |
| `net user`           | Shows the user accounts on the computer      |
| `net localgroup`     | Shows the local groups on the computer        |
| `arp`                | Shows the IP-to-Physical address translation tables |

This room focused on post-exploitation enumeration of a Linux or MS Windows machine. For enumeration related to Active Directory, we recommend that you join the **Enumerating AD** room.