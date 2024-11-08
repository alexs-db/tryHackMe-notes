Tcpdump Options Summary
# tcpdump Command Reference

| Command | Explanation | Example Output |
|---------|-------------|----------------|
| `tcpdump -r TwoPackets.pcap` | Displays captured packets without any additional arguments. | Shows detailed packet info, including timestamps, IP addresses, and TCP flags. |
| `tcpdump -r TwoPackets.pcap -q` | Quick output; provides brief packet information, displaying timestamps, source/destination IPs, and ports. | `18:59:59.979771 IP 104.18.12.149.https > g5000.45248: tcp 25` |
| `tcpdump -r TwoPackets.pcap -e` | Displays the link-level header, including MAC addresses, which is useful for analyzing specific protocols. | `18:59:59.979771 44:df:65:d8:fe:6c > 02:83:1e:40:5d:17, ethertype IPv4 (0x0800), length 91:` |
| `tcpdump -r TwoPackets.pcap -A` | Displays packet data in ASCII format, which is useful for text-based content. | Shows packets with ASCII representations of their contents. |
| `tcpdump -r TwoPackets.pcap -xx` | Displays packet data in hexadecimal format, showing octets in two hexadecimal digits for detailed inspection. | `0x0000: 0283 1e40 5d17 44df ... (hexadecimal view of the packet)` |
| `tcpdump -r TwoPackets.pcap -X` | Displays both hexadecimal and ASCII formats, providing a comprehensive view of the packets. | Combines both hex and ASCII outputs for thorough analysis. |

## Example Commands and Outputs

### 1. Displaying Basic Packet Info

```bash
tcpdump -r TwoPackets.pcap
```

**Output:**

```makefile
18:59:59.979771 IP 104.18.12.149.https > g5000.45248: Flags [P.], ...
```

### 2. Quick Output

```bash
tcpdump -r TwoPackets.pcap -q
```

**Output:**

```makefile
18:59:59.979771 IP 104.18.12.149.https > g5000.45248: tcp 25
```

### 3. Link-Level Header

```bash
tcpdump -r TwoPackets.pcap -e
```

**Output:**

```ruby
18:59:59.979771 44:df:65:d8:fe:6c > 02:83:1e:40:5d:17, ethertype IPv4 (0x0800), ...
```

### 4. Packet Data in ASCII

```bash
tcpdump -r TwoPackets.pcap -A
```

**Output:**

```css
18:59:59.979771 IP 104.18.12.149.https > g5000.45248: ...
E..M..@.5...)h.....BY.......|.;5}...........
```

### 5. Hexadecimal Format

```bash
tcpdump -r TwoPackets.pcap -xx
```

**Output:**

```yaml
0x0000:  0283 1e40 5d17 44df ...
```

### 6. Both Hexadecimal and ASCII

```bash
tcpdump -r TwoPackets.pcap -X
```

**Output:**

```yaml
0x0000:  4500 004d fbd8 4000 3506 ...
E..M..@.5..)h...
```