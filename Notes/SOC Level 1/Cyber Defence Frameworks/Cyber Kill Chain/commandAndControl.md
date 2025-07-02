## Command and Control (C2)

After achieving persistence and executing malware on a victim's machine, attackers establish a Command and Control (C2) channel to remotely control and manipulate the compromised system. This process is also referred to as C&C or C2 beaconing, describing the ongoing communication between the infected host and the attacker's C2 server.

The compromised endpoint communicates with an external server set up by the attacker to establish this channel. Once the connection is established, the attacker gains full control over the victim's machine. Historically, Internet Relay Chat (IRC) was commonly used for C2 channels, but modern security solutions can now easily detect malicious IRC traffic.

### Common C2 Channels

Attackers now use various protocols to evade detection:

- **HTTP (port 80) and HTTPS (port 443):** These protocols allow malicious traffic to blend in with legitimate web traffic, helping attackers bypass firewalls.
- **DNS (Domain Name System):** Infected machines make frequent DNS requests to attacker-controlled servers, a technique known as DNS tunneling.

> **Note:** The C2 infrastructure may be operated by the adversary directly or by another compromised host.