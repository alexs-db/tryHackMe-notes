# Evading Signature-Based IDS/IPS

Evading a signature-based IDS/IPS involves manipulating your traffic so it does not match known IDS/IPS signatures. Here are four general approaches to evade IDS/IPS systems:

1. **Evasion via Protocol Manipulation**
2. **Evasion via Payload Manipulation**
3. **Evasion via Route Manipulation**
4. **Evasion via Tactical Denial of Service (DoS)**

This guide focuses on evasion using `nmap`, `ncat`, and `socat`. Nmap evasion techniques are discussed in detail in the Firewalls room; here, the emphasis is on `ncat` and `socat` where appropriate.

Each approach is expanded in its own section. Let’s start with protocol manipulation.

---

## 1. Evasion via Protocol Manipulation

**Techniques include:**
- Relying on a different protocol
- Manipulating (source) TCP/UDP port
- Using session splicing (IP packet fragmentation)
- Sending invalid packets

### Relying on a Different Protocol

IDS/IPS systems may block certain protocols and allow others. For example, using UDP instead of TCP, or HTTP instead of DNS, can help evade detection. Knowledge of the target's allowed protocols is crucial.

**Example:**  
If web browsing is allowed, hosts can usually connect to ports 80 (HTTP) and 443 (HTTPS). Some organizations rely on specific services (e.g., Google web hosting), which attackers can exploit to conceal malicious activity. Trial and error may be necessary, but avoid creating too much noise.

**Scenario:**  
An IPS blocks DNS queries and HTTP requests, enforcing local DNS and secure HTTP (HTTPS) communications. In this case, tunneling traffic over HTTPS may help evade the IPS.

#### Using Ncat

- **Listen with TCP:**  
    ```bash
    ncat -lvnp PORT_NUM
    ```
- **Connect with TCP:**  
    ```bash
    ncat TARGET_IP PORT_NUM
    ```
    - `-l`: Listen for incoming connections
    - `-v`: Verbose output
    - `-n`: Avoid hostname resolution
    - `-p`: Specify port number

- **Listen with UDP:**  
    ```bash
    ncat -ulvnp PORT_NUM
    ```
- **Connect with UDP:**  
    ```bash
    nc -u TARGET_IP PORT_NUM
    ```

**Examples:**
- `ncat -lvnp 25` (TCP, appears as SMTP)
- `ncat -ulvnp 162` (UDP, appears as SNMP)

---

## 2. Manipulating (Source) TCP/UDP Port

TCP/UDP source and destination ports are often inspected by security solutions. Without deep packet inspection (DPI), port numbers indicate the service.

**Example with Nmap:**
- Make port scanning traffic appear as HTTP:
    ```bash
    nmap -sS -Pn -g 80 -F MACHINE_IP
    ```
- Make UDP scan appear as DNS:
    ```bash
    nmap -sU -Pn -g 53 -F MACHINE_IP
    ```

**Example with Ncat:**
- Listen as DNS server (UDP port 53):
    ```bash
    ncat -ulvnp 53
    ```
- Connect to UDP port 53:
    ```bash
    ncat -u ATTACKER_IP 53
    ```
- Listen as HTTP server (TCP port 80):
    ```bash
    ncat -lvnp 80
    ```
- Connect to TCP port 80:
    ```bash
    nc ATTACKER_IP 80
    ```

---

## 3. Session Splicing (IP Packet Fragmentation)

In IPv4, IP packet fragmentation (session splicing) can evade IDS signatures by splitting attack packets into smaller fragments. Unless the IDS reassembles packets, detection rules may not trigger.

**Nmap Options:**
- `-f`: Fragment packets into 8-byte segments
- `-ff`: Fragment into 16-byte segments
- `--mtu SIZE`: Custom fragment size (multiple of 8)

**Fragroute Example:**
- Create `fragroute.conf` with:
    ```
    ip_frag 16
    ```
- Run:
    ```bash
    fragroute -f fragroute.conf HOST
    ```

---

## 4. Sending Invalid Packets

Systems respond predictably to valid packets, but responses to invalid packets can vary. IDS/IPS may process invalid packets differently than target systems.

**Nmap Options:**
- `--badsum`: Send packets with invalid TCP/UDP checksums
- `--scanflags`: Set custom TCP flags (e.g., `SYNRSTFIN`)

**TCP Flags:**
- URG: Urgent
- ACK: Acknowledge
- PSH: Push
- RST: Reset
- SYN: Synchronize
- FIN: Finish

**hping3 Examples:**
- `-t` or `--ttl`: Set IP Time to Live
- `-b` or `--badsum`: Bad checksum
- `-S`, `-A`, `-P`, `-U`, `-F`, `-R`: Set TCP SYN, ACK, PUSH, URG, FIN, RST flags

Refer to the `hping3` manual for more options.

---