# TCPDump

## 1. Filtering Packet Length
- **greater LENGTH**: Displays packets with a length greater than or equal to LENGTH.
- **less LENGTH**: Displays packets with a length less than or equal to LENGTH.

## 2. Key Binary Operations
- **& (AND)**: Returns 1 only if both bits are 1. Example: `1 & 1 = 1`.
- **| (OR)**: Returns 1 if at least one bit is 1. Example: `1 | 0 = 1`.
- **! (NOT)**: Inverts the bit (1 becomes 0, and 0 becomes 1).

## 3. Filtering with Header Bytes
- **Syntax**: `proto[expr:size]`
    - **proto**: Protocol (e.g., arp, ether, ip, tcp).
    - **expr**: Byte offset (0 = first byte).
    - **size**: Number of bytes (optional, default is 1).

### Examples:
- `ether[0] & 1 != 0`: Captures Ethernet packets sent to a multicast address.
- `ip[0] & 0xf != 5`: Captures IP packets with options.

## 4. Filtering Based on TCP Flags
- **tcp[tcpflags]**: Used to filter based on TCP flags:
    - **tcp-syn**: SYN (Synchronize)
    - **tcp-ack**: ACK (Acknowledge)
    - **tcp-fin**: FIN (Finish)
    - **tcp-rst**: RST (Reset)
    - **tcp-push**: PUSH

### Example Commands:
- `tcpdump "tcp[tcpflags] == tcp-syn"`: Captures TCP packets with only the SYN flag set.
- `tcpdump "tcp[tcpflags] & tcp-syn != 0"`: Captures TCP packets with the SYN flag set.
- `tcpdump "tcp[tcpflags] & (tcp-syn|tcp-ack) != 0"`: Captures TCP packets with at least the SYN or ACK flag set.
