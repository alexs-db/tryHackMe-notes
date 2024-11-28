In this room, we learned how to detect the running services and their versions along with the host operating system. We learned how to enable traceroute and we covered selecting one or more scripts to aid in penetration testing. Finally, we covered the different formats to save the scan results for future reference. The table below summarizes the most important options we covered in this room.

| Option                  | Meaning                                      |
|-------------------------|----------------------------------------------|
| `-sV`                   | determine service/version info on open ports |
| `-sV --version-light`   | try the most likely probes (2)               |
| `-sV --version-all`     | try all available probes (9)                 |
| `-O`                    | detect OS                                    |
| `--traceroute`          | run traceroute to target                     |
| `--script=SCRIPTS`      | Nmap scripts to run                          |
| `-sC` or `--script=default` | run default scripts                     |
| `-A`                    | equivalent to `-sV -O -sC --traceroute`      |
| `-oN`                   | save output in normal format                 |
| `-oG`                   | save output in grepable format               |
| `-oX`                   | save output in XML format                    |
| `-oA`                   | save output in normal, XML and Grepable formats |
