In this room, we have covered many various tools. It is easy to put a few of them together via a shell script to build a primitive network and system scanner. You can use `traceroute` to map the path to the target, `ping` to check if the target system responds to ICMP Echo, and `telnet` to check which ports are open and reachable by attempting to connect to them. Available scanners do this at much more advanced and sophisticated levels, as we will see in the next four rooms with `nmap`.

### Command Examples

| Command       | Example                                    |
|---------------|--------------------------------------------|
| `ping`        | `ping -c 10 MACHINE_IP` on Linux or macOS  |
| `ping`        | `ping -n 10 MACHINE_IP` on MS Windows      |
| `traceroute`  | `traceroute MACHINE_IP` on Linux or macOS  |
| `tracert`     | `tracert MACHINE_IP` on MS Windows         |
| `telnet`      | `telnet MACHINE_IP PORT_NUMBER`            |
| `netcat` as client | `nc MACHINE_IP PORT_NUMBER`           |
| `netcat` as server | `nc -lvnp PORT_NUMBER`                |

Although these are fundamental tools, they are readily available on most systems. In particular, a web browser is installed on practically every computer and smartphone and can be an essential tool in your arsenal for conducting reconnaissance without raising alarms. If you want to gain more profound knowledge of the Developer Tools, we recommend joining Walking An Application.

### Developer Tools Shortcuts

| Operating System      | Developer Tools Shortcut |
|-----------------------|--------------------------|
| Linux or MS Windows   | `Ctrl+Shift+I`           |
| macOS                 | `Option + Command + I`   |
