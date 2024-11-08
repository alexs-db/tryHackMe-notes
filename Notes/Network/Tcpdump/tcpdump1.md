# Tcpdump Cheat Sheet

## Basic Commands

### Command without arguments
- Only tests installation.

### Specify Interface
- Use `-i INTERFACE` to listen on a particular network interface (e.g., `-i eth0`), or `-i any` for all interfaces.

### List interfaces
- Use `ip address show` or `ip a s` to see available interfaces.

## Saving and Reading Packet Captures

### Save Packets
- `-w FILE` writes captured packets to a file, typically `.pcap`, for later analysis.

### Read Packets from File
- `-r FILE` reads packets from a file, useful for inspecting protocol behavior or analyzing specific events.

## Controlling Packet Capture

### Packet Count
- `-c COUNT` limits the capture to a specific number of packets.

### Disable Resolution
- `-n`: Prevents domain resolution for IPs.
- `-nn`: Disables resolution of both domain names and protocol numbers.

## Verbose Output

### Verbose Levels
- `-v`, `-vv`, or `-vvv` increases output detail for deeper analysis.

## Quick Examples

### Capture 50 packets on eth0 with verbosity
```bash
tcpdump -i eth0 -c 50 -v
```

### Capture packets on WiFi and save to file
```bash
tcpdump -i wlo1 -w data.pcap
```

### Capture on all interfaces without resolution
```bash
tcpdump -i any -nn
```