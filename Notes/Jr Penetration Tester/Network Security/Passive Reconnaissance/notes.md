In this room, we focused on passive reconnaissance. In particular, we covered command-line tools, whois, nslookup, and dig. We also discussed two publicly available services DNSDumpster and Shodan.io. The power of such tools is that you can collect information about your targets without directly connecting to them. Moreover, the trove of information you may find using such tools can be massive once you master the search options and get used to reading the results.

| Purpose                        | Commandline Example                       |
|--------------------------------|-------------------------------------------|
| Lookup WHOIS record            | `whois tryhackme.com`                     |
| Lookup DNS A records           | `nslookup -type=A tryhackme.com`          |
| Lookup DNS MX records at DNS server | `nslookup -type=MX tryhackme.com 1.1.1.1` |
| Lookup DNS TXT records         | `nslookup -type=TXT tryhackme.com`        |
| Lookup DNS A records           | `dig tryhackme.com A`                     |
| Lookup DNS MX records at DNS server | `dig @1.1.1.1 tryhackme.com MX`         |
| Lookup DNS TXT records         | `dig tryhackme.com TXT`                   |