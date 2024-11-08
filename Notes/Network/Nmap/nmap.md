Nmap Overview
# Nmap Usage Guide

In this session, we explored how to effectively use Nmap to discover live hosts on any network. We covered:

## Port Scanning
- Different types of port scans and how to find service version numbers.

## Scan Timing Control
- How to manage the timing of scans to optimize efficiency and stealth.

## Saving Results
- Various formats for saving Nmap scan results for later analysis.

## Running Nmap with Privileges
- **Root Privileges**: It’s best to run Nmap with `sudo` to access its full range of features. For example, using a SYN scan (`-sS`) requires root access, while a TCP connect scan (`-sT`) is the default for non-root users.
- Running Nmap as a local user will limit functionality and provide only a fraction of its capabilities.

## Key Nmap Options
Here's a quick reference table of important Nmap options covered in this session:

| Option | Explanation |
|--------|-------------|
| **Host Discovery** | |
| `-sL` | List scan – list targets without scanning |
| `-sn` | Ping scan – host discovery only |
| **Port Scanning** | |
| `-sT` | TCP connect scan – complete three-way handshake |
| `-sS` | TCP SYN – only first step of the handshake |
| `-sU` | UDP Scan |
| `-F` | Fast mode – scans the 100 most common ports |
| `-p[range]` | Specifies a range of port numbers (e.g., `-p-` scans all ports) |
| `-Pn` | Treat all hosts as online – scan hosts that appear to be down |
| **Service Detection** | |
| `-O` | OS detection |
| `-sV` | Service version detection |
| `-A` | OS detection, version detection, and other options |
| **Timing** | |
| `-T<0-5>` | Timing template – from paranoid (0) to insane (5) |
| `--min-parallelism <numprobes>` | Minimum number of parallel probes |
| `--max-parallelism <numprobes>` | Maximum number of parallel probes |
| `--min-rate <number>` | Minimum packets per second |
| `--max-rate <number>` | Maximum packets per second |
| `--host-timeout` | Max time to wait for a target host |
| **Real-time Output** | |
| `-v` | Verbosity level (e.g., `-vv`, `-v4`) |
| `-d` | Debugging level (e.g., `-d`, `-d9`) |
| **Report Formats** | |
| `-oN <filename>` | Normal output |
| `-oX <filename>` | XML output |
| `-oG <filename>` | Grep-able output |
| `-oA <basename>` | Output in all major formats |