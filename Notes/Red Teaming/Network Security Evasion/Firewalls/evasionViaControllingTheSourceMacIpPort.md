# Evasion via Controlling the Source MAC/IP/Port

When scanning a host behind a firewall, the firewall will usually detect and block port scans. To bypass this, you need to adapt your network and port scan techniques. Tools like **Nmap** provide several features to help evade firewalls. In this guide, Nmap techniques are grouped into three categories:

1. **Evasion via controlling the source MAC/IP/Port**
2. **Evasion via fragmentation, MTU, and data length**
3. **Evasion through modifying header fields**

Nmap allows you to hide or spoof the source using:

- Decoy(s)
- Proxy
- Spoofed MAC Address
- Spoofed Source IP Address
- Fixed Source Port Number

---

## Example: Nmap Stealth (SYN) Scan

Before diving into each approach, let's look at a typical Nmap stealth (SYN) scan. In this example, we scan a Windows target (with the default built-in firewall). The `-Pn` option is used to skip host discovery and force the scan even if no ping reply is received. To speed up the scan, we limit it to the 100 most common ports using the `-F` option.

**Command used:**
```bash
nmap -sS -Pn -F MACHINE_IP
```

Wireshark was running on the same system as Nmap to capture the probe packets.

---

## Key Observations

- The source IP address (`10.14.17.226`) generated and sent around 200 packets. The `-F` option limits the scan to the top 100 common ports; each port receives a second SYN packet if the first does not get a reply.
- The source port number is chosen at random (e.g., `37710`).
- The total length of the IP packet is 44 bytes (20 bytes for the IP header, 24 bytes for the TCP header, and no TCP data).
- The Time to Live (TTL) is 42.
- No errors are introduced in the checksum.

---

In the following sections, we will explore how Nmap provides various options to evade firewalls and other network security solutions.