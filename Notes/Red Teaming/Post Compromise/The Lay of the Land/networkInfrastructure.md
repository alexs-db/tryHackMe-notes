# The Lay of the Land: Network Infrastructure

Once arriving onto an unknown network, our first goal is to identify where we are and what we can access. During a red team engagement, understanding the target system, its services, and the network type is crucial. Enumeration of the compromised machine after gaining initial access is key to answering these questions. This section discusses common types of networks encountered during engagements.

---

## Network Segmentation

Network segmentation is an additional layer of network security that divides a network into multiple subnets. It improves security and management by preventing unauthorized access to critical assets such as customer data and financial records.

### Virtual Local Area Networks (VLANs)

VLANs are a network segmentation technique used to address issues like broadcasting within a local network and to enhance security. Hosts within a VLAN can only communicate with other hosts in the same VLAN.

> **Tip:** To learn more about network fundamentals, check out the [TryHackMe Network Fundamentals module](https://tryhackme.com).

---

## Internal Networks

Internal networks are subnetworks segmented based on the importance of devices or data accessibility. Their primary purpose is to facilitate information sharing, communication, collaboration, and operational systems within an organization. Network administrators use segmentation to control traffic, optimize performance, and improve security.

### Example of Network Segmentation

A simple example of network segmentation divides the network into two parts:
1. Employee workstations and personal devices.
2. Private internal network devices providing services like DNS, internal web, and email.

---

## Demilitarized Zone (DMZ)

A DMZ network is an edge network that adds an extra security layer between a corporation's internal local-area network and untrusted traffic. It is commonly designed as a subnetwork between the public internet and internal networks.

### Example of DMZ Design

- **Red traffic:** Untrusted traffic from the internet.
- **Green traffic:** Controlled traffic between the internal network and the DMZ, often passing through one or more security devices.

---

## Network Enumeration

Enumeration is the discovery phase where attackers gather information about the system and internal network. This information is used for lateral movement or privilege escalation within the system or Active Directory (AD) environment.

### Enumerating Networking Aspects

Key areas to check include:
- TCP and UDP ports
- Established connections
- Routing tables
- ARP tables

#### Example: Checking Open Ports

```bash
netstat -na
```

**Output:**
```
Active Connections

    Proto  Local Address          Foreign Address        State
    TCP    0.0.0.0:80             0.0.0.0:0              LISTENING
    TCP    0.0.0.0:88             0.0.0.0:0              LISTENING
    TCP    0.0.0.0:135            0.0.0.0:0              LISTENING
    TCP    0.0.0.0:389            0.0.0.0:0              LISTENING
```

#### Example: Listing the ARP Table

```bash
arp -a
```

**Output:**
```
Interface: 10.10.141.51 --- 0xa
    Internet Address      Physical Address      Type
    10.10.0.1             02-c8-85-b5-5a-aa     dynamic
    10.10.255.255         ff-ff-ff-ff-ff-ff     static
```

---

## Internal Network Services

Internal network services provide private communication access for internal devices. Examples include internal DNS, web servers, and custom applications. These services are not accessible outside the network but become reachable once initial access to the network is gained.

> **Note:** More details on Windows applications and services, including DNS and custom web applications, will be discussed in Task 9.