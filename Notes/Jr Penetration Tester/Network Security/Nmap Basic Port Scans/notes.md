## Nmap Basic Port Scans

This room covered three types of scans.

### Port Scan Types

| Port Scan Type  | Example Command            |
|-----------------|-----------------------------|
| TCP Connect Scan| `nmap -sT MACHINE_IP`       |
| TCP SYN Scan    | `sudo nmap -sS MACHINE_IP`  |
| UDP Scan        | `sudo nmap -sU MACHINE_IP`  |

These scan types should get you started discovering running TCP and UDP services on a target host.

### Options and Purposes

| Option                | Purpose                                      |
|-----------------------|----------------------------------------------|
| `-p-`                 | all ports                                    |
| `-p1-1023`            | scan ports 1 to 1023                         |
| `-F`                  | 100 most common ports                        |
| `-r`                  | scan ports in consecutive order              |
| `-T<0-5>`             | `-T0` being the slowest and `T5` the fastest |
| `--max-rate 50`       | rate <= 50 packets/sec                       |
| `--min-rate 15`       | rate >= 15 packets/sec                       |
| `--min-parallelism 100` | at least 100 probes in parallel            |