# Introduction to Intrusion Detection and Prevention Systems

An **Intrusion Detection System (IDS)** is designed to detect network or system intrusions. Think of it as a guard monitoring live feeds from security cameras: the guard can spot a theft but cannot stop it alone. If the guard can alert another guard to intervene, detection becomes prevention. This is the role of an **Intrusion Detection and Prevention System (IDPS)** or simply an **Intrusion Prevention System (IPS)**—a system that can both detect and prevent intrusions.

Understanding the difference between detection and prevention is essential:

- **Detection:** Identifying suspicious or malicious activity.
- **Prevention:** Taking action to block or stop the detected activity.

**Snort** is a popular network intrusion detection and prevention system. It can be configured as either an IDS or an IPS. To function as an IPS, Snort must be set up inline, bridging two or more network interfaces to actively block (drop) offending connections.

As a signature-based network IDS, Snort operates as shown in the figure below:

<!-- Insert IDS diagram here -->

Snort can also be configured as an IPS when set up inline:

<!-- Insert IPS diagram here -->

## Types of IDS

IDS setups can be categorized based on their location in the network:

- **Host-based IDS (HIDS):** Installed on an operating system alongside other applications. HIDS can monitor traffic to and from the host and observe running processes.
- **Network-based IDS (NIDS):** A dedicated appliance or server that monitors network traffic. NIDS should be connected to a monitor port on a switch to observe all traffic on the network or specific VLANs. It processes this traffic to detect malicious activity.

The figure below illustrates the difference in coverage between HIDS and NIDS, using two red circles for comparison:

<!-- Insert HIDS vs NIDS coverage diagram here -->
