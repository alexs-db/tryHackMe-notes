# IDS/IPS Rule Syntax and Examples

Each IDS/IPS has its own syntax for writing rules. For example, **Snort** uses the following format:

```
Rule Header (Rule Options)
```

### Rule Header Components

- **Action:** Examples include `alert`, `log`, `pass`, `drop`, and `reject`.
- **Protocol:** `TCP`, `UDP`, `ICMP`, or `IP`.
- **Source IP/Source Port:**  
    Example: `!10.10.0.0/16 any` refers to everything *not* in the class B subnet `10.10.0.0/16`.
- **Direction of Flow:**  
    - `->` indicates left (source) to right (destination).
    - `<>` indicates bi-directional traffic.
- **Destination IP/Destination Port:**  
    Example: `10.10.0.0/16 any` refers to the class B subnet `10.10.0.0/16`.

---

## Example: Dropping All ICMP Traffic

```snort
drop icmp any any -> any any (msg: "ICMP Ping Scan"; dsize:0; sid:1000020; rev:1;)
```

This rule instructs Snort to **drop any ICMP packet** from any source IP (any port) to any destination IP (any port). The log message will be `"ICMP Ping Scan"`.

---

## Example: Detecting Exploitation via HTTP POST

Suppose a vulnerability exists in a web server's handling of HTTP POST requests, allowing attackers to run system commands. To detect exploitation attempts using `ncat`, you can create a Snort rule to look for the term `ncat` in the payload.

### Basic Rule

```snort
alert tcp any any <> any 80 (msg: "Netcat Exploitation"; content:"ncat"; sid:1000030; rev:1;)
```

This rule inspects packets exchanged with port 80 for the string `ncat`.

### Hexadecimal Content

Alternatively, you can specify the content in hexadecimal.  
`ncat` in ASCII is `6e 63 61 74`, encapsulated with pipes (`|`):

```snort
alert tcp any any <> any 80 (msg: "Netcat Exploitation"; content:"|6e 63 61 74|"; sid:1000031; rev:1;)
```

### Refined Rule for HTTP POST Requests

If you expect to see `ncat` in HTTP POST requests, refine the rule:

```snort
alert tcp any any <> any 80 (msg: "Netcat Exploitation"; flow:established,to_server; content:"POST"; nocase; http_method; content:"ncat"; nocase; sid:1000032; rev:1;)
```

- `flow:established` tells Snort to look at streams started by a TCP 3-way handshake (established connections).

---

## Example Snort Logs

```
[**] [1:1000031:1] Netcat Exploitation [**]
[Priority: 0] 
01/14-12:51:26.717401 10.14.17.226:45480 -> 10.10.112.168:80
TCP TTL:63 TOS:0x0 ID:34278 IpLen:20 DgmLen:541 DF
***AP*** Seq: 0x26B5C2F  Ack: 0x0  Win: 0x0  TcpLen: 32

[**] [1:1000031:1] Netcat Exploitation [**]
[Priority: 0] 
01/14-12:51:26.717401 10.14.17.226:45480 -> 10.10.112.168:80
TCP TTL:63 TOS:0x0 ID:34278 IpLen:20 DgmLen:541 DF
***AP*** Seq: 0x26B5C2F  Ack: 0xF1090882  Win: 0x3F  TcpLen: 32
TCP Options (3) => NOP NOP TS: 2244530364 287085341
```

---

## Limitations of Signature-Based IDS/IPS

- If an attacker modifies the payload to avoid using `ncat` verbatim, the attack may go undetected.
- Signature-based IDS/IPS are only as effective as their rules and how frequently they are updated.

> **Note:** Evasion techniques are discussed in the next section.