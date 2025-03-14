## Nmap Advanced Port Scans

This room covered the following types of scans.

| Port Scan Type       | Example Command                                      |
|----------------------|------------------------------------------------------|
| TCP Null Scan        | `sudo nmap -sN MACHINE_IP`                           |
| TCP FIN Scan         | `sudo nmap -sF MACHINE_IP`                           |
| TCP Xmas Scan        | `sudo nmap -sX MACHINE_IP`                           |
| TCP Maimon Scan      | `sudo nmap -sM MACHINE_IP`                           |
| TCP ACK Scan         | `sudo nmap -sA MACHINE_IP`                           |
| TCP Window Scan      | `sudo nmap -sW MACHINE_IP`                           |
| Custom TCP Scan      | `sudo nmap --scanflags URGACKPSHRSTSYNFIN MACHINE_IP`|
| Spoofed Source IP    | `sudo nmap -S SPOOFED_IP MACHINE_IP`                 |
| Spoofed MAC Address  | `--spoof-mac SPOOFED_MAC`                            |
| Decoy Scan           | `nmap -D DECOY_IP,ME MACHINE_IP`                     |
| Idle (Zombie) Scan   | `sudo nmap -sI ZOMBIE_IP MACHINE_IP`                 |
| Fragment IP data into 8 bytes | `-f`                                        |
| Fragment IP data into 16 bytes | `-ff`                                      |

### Additional Options

| Option               | Purpose                                              |
|----------------------|------------------------------------------------------|
| `--source-port PORT_NUM` | specify source port number                       |
| `--data-length NUM`  | append random data to reach given length             |
| `--reason`           | explains how Nmap made its conclusion                |
| `-v`                 | verbose                                              |
| `-vv`                | very verbose                                         |
| `-d`                 | debugging                                            |
| `-dd`                | more details for debugging                           |

These scan types rely on setting TCP flags in unexpected ways to prompt ports for a reply. Null, FIN, and Xmas scans provoke a response from closed ports, while Maimon, ACK, and Window scans provoke a response from open and closed ports.
