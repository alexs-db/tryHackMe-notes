# Types of Firewalls

Firewalls can be classified in several ways, including by their deployment method, intended use, and inspection capabilities.

## By Deployment

### Hardware Firewall (Appliance Firewall)
A hardware firewall is a dedicated device through which network traffic passes. Examples include:
- Cisco ASA (Adaptive Security Appliance)
- WatchGuard Firebox
- Netgate pfSense Plus appliance

### Software Firewall
A software firewall is installed on an operating system, either built-in or as an additional service. Examples include:
- Windows Defender Firewall (built into Microsoft Windows)
- Linux `iptables` and `firewalld`

---

## By Intended Use

### Personal Firewall
Designed to protect a single system or a small network (e.g., home networks). Examples:
- Bitdefender BOX
- Built-in firewalls in home routers from Linksys, D-Link, etc.

### Commercial Firewall
Protects medium-to-large networks, offering higher reliability, processing power, and bandwidth support. Commonly used in organizations, universities, and enterprises.

---

## By Inspection Capabilities

Firewalls can also be classified based on their inspection abilities, often in relation to the OSI model layers. Traditional firewalls focus on layers 3 (Network) and 4 (Transport), while next-generation firewalls can inspect up to layer 7 (Application).

### Packet-Filtering Firewall
- Inspects protocol, source/destination IP addresses, and ports (TCP/UDP).
- Stateless inspection.

### Circuit-Level Gateway
- Adds capabilities such as checking the TCP three-way handshake against firewall rules.

### Stateful Inspection Firewall
- Tracks established TCP sessions.
- Can detect and block packets outside established sessions.

### Proxy Firewall (Application Firewall / Web Application Firewall - WAF)
- Acts as an intermediary, inspecting packet payloads.
- Commonly used for web applications.

### Next-Generation Firewall (NGFW)
- Monitors all OSI layers (2–7).
- Provides application awareness and control.
- Examples: Juniper SRX series, Cisco Firepower.

### Cloud Firewall (Firewall as a Service - FWaaS)
- Replaces hardware firewalls in cloud environments.
- Offers scalability and features comparable to NGFW, depending on the provider.
- Examples: Cloudflare Magic Firewall, Juniper vSRX, AWS WAF (web application protection), AWS Shield (DDoS protection).

---