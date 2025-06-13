## Conclusion

This room covered the different types of firewalls and common evasion techniques. Understanding the limitations of the firewall technology you are targeting helps you choose and construct suitable firewall evasion processes. While this room demonstrated various evasion techniques using `ncat`, similar results can be achieved with other tools such as `socat`. It is recommended to check out the **What the Shell?** room for further learning.

### Summary of Nmap Evasion Arguments

| Evasion Approach                                 | Nmap Argument(s)                                 |
|--------------------------------------------------|--------------------------------------------------|
| Hide a scan with decoys                          | `-D DECOY1_IP1,DECOY_IP2,ME`                     |
| Use an HTTP/SOCKS4 proxy to relay connections    | `--proxies PROXY_URL`                            |
| Spoof source MAC address                         | `--spoof-mac MAC_ADDRESS`                        |
| Spoof source IP address                          | `-S IP_ADDRESS`                                  |
| Use a specific source port number                | `-g PORT_NUM` or `--source-port PORT_NUM`        |
| Fragment IP data into 8 bytes                    | `-f`                                             |
| Fragment IP data into 16 bytes                   | `-ff`                                            |
| Fragment packets with given MTU                  | `--mtu VALUE`                                    |
| Specify packet length                            | `--data-length NUM`                              |
| Set IP time-to-live field                        | `--ttl VALUE`                                    |
| Send packets with specified IP options           | `--ip-options OPTIONS`                           |
| Send packets with a wrong TCP/UDP checksum       | `--badsum`                                       |