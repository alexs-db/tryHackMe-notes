# Traffic Analysis / Network Traffic Analysis

Traffic Analysis is the process of intercepting, recording, monitoring, and analyzing network data and communication patterns to detect and respond to system health issues, network anomalies, and threats. The network is a rich data source, making traffic analysis valuable for both security and operational purposes.

**Operational issues** include system availability checks and performance measurement.  
**Security issues** involve detecting anomalies and suspicious activity on the network.

Traffic analysis is a key approach in network security and is part of several disciplines:

- **Network Sniffing and Packet Analysis** (see: Wireshark)
- **Network Monitoring** (see: Zeek)
- **Intrusion Detection and Prevention** (see: Snort)
- **Network Forensics** (see: NetworkMiner)
- **Threat Hunting** (see: Brim)

## Main Techniques in Traffic Analysis

| Technique        | Description                                                                 | Advantage                                   | Challenge                                      |
|------------------|-----------------------------------------------------------------------------|---------------------------------------------|------------------------------------------------|
| **Flow Analysis**   | Collects data/evidence from networking devices. Provides statistical results through data summaries without deep packet inspection. | Easy to collect and analyze                 | Lacks full packet details for root cause analysis |
| **Packet Analysis** | Collects all available network data. Involves in-depth packet-level investigation (Deep Packet Inspection, DPI) to detect and block anomalous/malicious packets. | Provides full packet details for root cause analysis | Requires time and expertise to analyze           |

## Benefits of Traffic Analysis

- Provides full network visibility
- Enables comprehensive baselining for asset tracking
- Helps detect and respond to anomalies and threats

## Does Traffic Analysis Still Matter?

Despite the widespread use of security tools and the shift to cloud computing, attackers continue to adapt their tactics to evade detection. Network data remains a valuable source—even when encoded or encrypted—by revealing odd or unexpected patterns. Traffic analysis is an essential skill for any security analyst aiming to detect and respond to advanced threats.

---

Now that you understand Traffic Analysis and its operation, use the static site to simulate a traffic analysis operation and find the flags.