## Network Artifacts (Annoying)

Network artifacts belong to the yellow zone in the Pyramid of Pain. Detecting and responding to these threats forces attackers to spend more time changing their tactics or modifying their tools, giving defenders more time to respond and remediate.

A network artifact can include:

- **User-Agent strings**
- **C2 (Command and Control) information**
- **URI patterns in HTTP POST requests**

For example, an attacker might use a User-Agent string that is unusual or has not been observed in your environment. According to [RFC2616](https://datatracker.ietf.org/doc/html/rfc2616), the User-Agent is a request-header field containing information about the user agent originating the request.

### Detecting Network Artifacts

Network artifacts can be detected in PCAP files (which contain network packet data) using tools such as:

- **Wireshark**
- **TShark** (command-line version of Wireshark)
- **IDS logs** (e.g., from Snort)

#### Example: Filtering User-Agent Strings with TShark

To extract User-Agent strings from HTTP requests in a PCAP file, use:

```sh
tshark --Y http.request -T fields -e http.host -e http.user_agent -r analysis_file.pcap
```

### Emotet Downloader Trojan Example

The Emotet Downloader Trojan often uses specific User-Agent strings. If you can detect these custom User-Agent strings, you may be able to block them, creating more obstacles for attackers and making their attempts to compromise the network more difficult.
