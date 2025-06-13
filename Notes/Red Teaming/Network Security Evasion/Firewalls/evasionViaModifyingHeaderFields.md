# Evasion via Modifying Header Fields

Nmap allows you to control various header fields to help evade firewalls. Here are some techniques:

## Set IP Time-to-Live (TTL)

Nmap gives you control over the Time-to-Live (TTL) field in the IP header. You can use the `--ttl VALUE` option to set a custom TTL. This can be useful if you think the default TTL exposes your port scan activities.

**Example:**
```bash
nmap -sS -Pn --ttl 81 -F MACHINE_IP
```
Packets with a custom TTL can be observed in Wireshark when running the above scan.

---

### Task: Scan with Custom TTL

1. **Scan with `--ttl 1`:**  
    Start the AttackBox and the attached machine. After both are fully loaded, scan the MS Windows machine using the `--ttl 1` option. Check the number of open ports. (No answer needed.)

2. **Scan with `--ttl 2`:**  
    Scan the MS Windows machine using the `--ttl 2` option.  
    **Question:** How many ports appear to be open?  
    **Answer:**  
    _

---

## Set IP Options

The IP Options field in the IP header can be controlled using the `--ip-options HEX_STRING` option. Each byte is written as `\xHH`, where `HH` is a two-digit hexadecimal value.

**Shortcuts:**
- `R` – Record-route
- `T` – Record-timestamp
- `U` – Record-route and record-timestamp
- `L` – Loose source routing (followed by IP addresses)
- `S` – Strict source routing (followed by IP addresses)

Loose and strict source routing can help direct packets along specific routes to bypass certain security systems.

---

## Use a Wrong Checksum

You can send packets with an intentionally incorrect checksum using the `--badsum` option. Some systems drop packets with bad checksums, while others do not. This can help you learn more about the systems on your network.

**Example:**
```bash
nmap -sS -Pn --badsum -F MACHINE_IP
```
Sample output:
```
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2022-01-28 16:07 EET
Nmap scan report for MACHINE_IP
Host is up.
All 100 scanned ports on MACHINE_IP are filtered

Nmap done: 1 IP address (1 host up) scanned in 21.31 seconds
```
Wireshark can highlight checksum errors if configured to verify checksums.

---

### Task: Scan with Bad Checksum

Scan the attached MS Windows machine using the `--badsum` option.  
**Question:** How many ports appear to be open?  
**Answer:**  
_

---

## Summary Table

| Evasion Approach                        | Nmap Argument                |
|-----------------------------------------|------------------------------|
| Set IP time-to-live field               | `--ttl VALUE`                |
| Send packets with specified IP options  | `--ip-options OPTIONS`       |
| Send packets with a wrong TCP/UDP checksum | `--badsum`                |