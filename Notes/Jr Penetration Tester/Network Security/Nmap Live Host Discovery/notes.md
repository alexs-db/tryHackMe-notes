## Enumerating Targets

We mentioned the different techniques we can use for scanning in Task 1. Before we explain each in detail and put it into use against a live target, we need to specify the targets we want to scan. Generally speaking, you can provide a list, a range, or a subnet. Examples of target specification are:

- **List**: `MACHINE_IP scanme.nmap.org example.com` will scan 3 IP addresses.
- **Range**: `10.11.12.15-20` will scan 6 IP addresses: `10.11.12.15`, `10.11.12.16`, … and `10.11.12.20`.
- **Subnet**: `MACHINE_IP/30` will scan 4 IP addresses.

You can also provide a file as input for your list of targets: `nmap -iL list_of_hosts.txt`.

If you want to check the list of hosts that Nmap will scan, you can use `nmap -sL TARGETS`. This option will give you a detailed list of the hosts that Nmap will scan without scanning them; however, Nmap will attempt a reverse-DNS resolution on all the targets to obtain their names. Names might reveal various information to the pentester. (If you don’t want Nmap to use the DNS server, you can add `-n`.)

## Nmap Host Discovery Using ARP

How would you know which hosts are up and running? It is essential to avoid wasting our time port-scanning an offline host or an IP address not in use. There are various ways to discover online hosts. When no host discovery options are provided, Nmap follows the following approaches to discover live hosts:

- When a privileged user tries to scan targets on a local network (Ethernet), Nmap uses ARP requests. A privileged user is root or a user who belongs to sudoers and can run sudo.
- When a privileged user tries to scan targets outside the local network, Nmap uses ICMP echo requests, TCP ACK (Acknowledge) to port 80, TCP SYN (Synchronize) to port 443, and ICMP timestamp request.
- When an unprivileged user tries to scan targets outside the local network, Nmap resorts to a TCP 3-way handshake by sending SYN packets to ports 80 and 443.

Nmap, by default, uses a ping scan to find live hosts, then proceeds to scan live hosts only. If you want to use Nmap to discover online hosts without port-scanning the live systems, you can issue `nmap -sn TARGETS`. Let’s dig deeper to gain a solid understanding of the different techniques used.

ARP scan is possible only if you are on the same subnet as the target systems. On an Ethernet (802.3) and WiFi (802.11), you need to know the MAC address of any system before you can communicate with it. The MAC address is necessary for the link-layer header; the header contains the source MAC address and the destination MAC address among other fields. To get the MAC address, the OS sends an ARP query. A host that replies to ARP queries is up. The ARP query only works if the target is on the same subnet as yourself, i.e., on the same Ethernet/WiFi. You should expect to see many ARP queries generated during a Nmap scan of a local network. If you want Nmap only to perform an ARP scan without port-scanning, you can use `nmap -PR -sn TARGETS`, where `-PR` indicates that you only want an ARP scan. The following example shows Nmap using ARP for host discovery without any port scanning. We run `nmap -PR -sn MACHINE_IP/24` to discover all the live systems on the same subnet as our target machine.


## Summary 

You have learned how ARP, ICMP, TCP, and UDP can detect live hosts by completing this room. Any response from a host is an indication that it is online. Below is a quick summary of the command-line options for Nmap that we have covered.

### Scan Types and Example Commands

| Scan Type             | Example Command                              |
|-----------------------|----------------------------------------------|
| ARP Scan              | `sudo nmap -PR -sn MACHINE_IP/24`            |
| ICMP Echo Scan        | `sudo nmap -PE -sn MACHINE_IP/24`            |
| ICMP Timestamp Scan   | `sudo nmap -PP -sn MACHINE_IP/24`            |
| ICMP Address Mask Scan| `sudo nmap -PM -sn MACHINE_IP/24`            |
| TCP SYN Ping Scan     | `sudo nmap -PS22,80,443 -sn MACHINE_IP/30`   |
| TCP ACK Ping Scan     | `sudo nmap -PA22,80,443 -sn MACHINE_IP/30`   |
| UDP Ping Scan         | `sudo nmap -PU53,161,162 -sn MACHINE_IP/30`  |

Remember to add `-sn` if you are only interested in host discovery without port-scanning. Omitting `-sn` will let Nmap default to port-scanning the live hosts.

### Options and Their Purposes

| Option | Purpose                              |
|--------|--------------------------------------|
| `-n`   | no DNS lookup                        |
| `-R`   | reverse-DNS lookup for all hosts     |
| `-sn`  | host discovery only                  |
