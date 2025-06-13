# Evasion via Forcing Fragmentation, MTU, and Data Length

You can control the packet size to evade firewalls and IDS/IPS by:

- **Fragmenting packets** (optionally with a given MTU). If the firewall or IDS/IPS does not reassemble the packet, it will likely let it pass. The target system will then reassemble and process it.
- **Sending packets with specific data lengths**.

---

## Fragment Your Packets with 8 Bytes of Data

One way to fragment your packets is to use the `-f` option. This fragments the IP packet to carry only 8 bytes of data. For example, a typical Nmap TCP port scan sends an IP packet with a 24-byte TCP header. Limiting the IP data to 8 bytes means the 24 bytes will be split across 3 IP packets.

**Example command:**
```sh
nmap -sS -Pn -f -F MACHINE_IP
```
Each IP packet is fragmented into three packets, each with 8 bytes of data.

**Question:**  
*What is the size of the IP packet when running Nmap with the `-f` option?*

```
__
```
<sub>Submit</sub>

---

## Fragment Your Packets with 16 Bytes of Data

The `-ff` option limits the IP data to 16 bytes. For a 24-byte TCP header, this results in two IP packets: one with 16 bytes and one with 8 bytes.

**Example command:**
```sh
nmap -sS -Pn -ff -F MACHINE_IP
```

**Question:**  
*What is the maximum size of the IP packet when running Nmap with the `-ff` option?*

```
__
```
<sub>Submit</sub>

---

## Fragment Your Packets According to a Set MTU

You can set the MTU in Nmap using `--mtu VALUE`, which specifies the number of bytes per IP packet (excluding the IP header). The value must be a multiple of 8.

> **Note:** The Maximum Transmission Unit (MTU) is the largest packet size that can pass on a link-layer connection (e.g., Ethernet MTU is 1500 bytes). Do not confuse this with Nmap's `--mtu` option.

**Example command:**
```sh
nmap -sS -Pn --mtu 8 -F MACHINE_IP
```
This is identical to using `-f`.

**Question:**  
*What is the maximum size of the IP packet when running Nmap with `--mtu 36` option?*

```
__
```
<sub>Submit</sub>

---

## Generate Packets with Specific Length

If packet size triggers detection, you can set a specific length using `--data-length VALUE`. The length should be a multiple of 8.

**Example command:**
```sh
nmap -sS -Pn --data-length 64 -F MACHINE_IP
```
Each TCP segment is padded with random data to reach 64 bytes.

**Question:**  
*What is the maximum size of the IP packet when running Nmap with `--data-length 128` option?*

```
___
```
<sub>Submit</sub>

---

## Summary Table

| Evasion Approach                  | Nmap Argument         |
|-----------------------------------|----------------------|
| Fragment IP data into 8 bytes     | `-f`                 |
| Fragment IP data into 16 bytes    | `-ff`                |
| Fragment packets with given MTU   | `--mtu VALUE`        |
| Specify packet length             | `--data-length NUM`  |

