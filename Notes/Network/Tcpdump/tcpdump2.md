# Basic Usage

Run without arguments to check installation, but specify capture details in real scenarios.

## Specify Network Interface

Use `-i INTERFACE` to capture packets on a specific interface (e.g., `eth0`).
Use `ip address show` or `ip a s` to list interfaces.

## Save & Read Packets

Save with `-w FILE` (e.g., `.pcap` files for analysis in Wireshark).
Read saved packets with `-r FILE`.

## Limit Packet Capture

Set capture limit with `-c COUNT` for specific packet counts.
Stop IP and port resolutions with `-n` and `-nn` options.

## Verbose Output

Use `-v` for more details, `-vv` and `-vvv` for further verbosity.

## Filtering Examples

### Filter by Host

Capture packets for a specific host: `tcpdump host IP/HOSTNAME`.
Source or destination filter: `src host IP` or `dst host IP`.

### Filter by Port

Capture traffic on a port (e.g., DNS on port 53): `tcpdump port PORT_NUMBER`.
Use `src port` or `dst port` for source or destination filtering.

### Filter by Protocol

Filter by protocol type: `tcpdump PROTOCOL` (e.g., `icmp`).

### Logical Operators

Combine conditions:
- `and`: Both conditions true (`tcpdump host 1.1.1.1 and tcp`).
- `or`: Either condition true (`tcpdump udp or icmp`).
- `not`: Excludes condition (`tcpdump not tcp`).