# Introduction to Firewalls

A **firewall** is software or hardware that monitors network traffic and compares it against a set of rules before allowing or blocking it. One simple analogy is a guard or gatekeeper at the entrance of an event, checking IDs against a set of rules before letting people enter or leave.

Before diving deeper into firewalls, it's helpful to recall the contents of an IP packet and TCP segment. The figure below (not shown) illustrates the fields found in an IP header. While the header may seem complex, we are mainly interested in a few fields. Different types of firewalls can inspect various packet fields; however, the most basic firewall should be able to inspect at least the following:

- **Protocol**
- **Source Address**
- **Destination Address**

Depending on the protocol field, the data in the IP datagram can be one of several options. Three common protocols are:

- **TCP**
- **UDP**
- **ICMP**

For TCP or UDP, the firewall should also be able to check the TCP and UDP headers for:

- **Source Port Number**
- **Destination Port Number**

The TCP header (see figure below, not shown) contains many fields that a firewall might analyze. Even the most basic firewalls should allow administrators to control access based on source and destination port numbers.

---

## Learning Objectives

This room covers:

- The different types of firewalls, according to various classification criteria
- Techniques to evade firewalls

**Prerequisites:**  
You should have basic knowledge of:

- ISO/OSI layers and TCP/IP layers (see the Network Fundamentals module for a refresher)
- Network and port scanning (see the Nmap module)
- Reverse and bind shells (see the "What the Shell?" room)

---

## Warmup Questions

The design logic of traditional firewalls is that a port number identifies the service and protocol. In traditional (packet-filtering) firewalls, traffic is allowed or blocked mainly based on:

- Protocol (TCP, UDP, ICMP)
- IP source address
- IP destination address
- Source TCP or UDP port number
- Destination TCP or UDP port number

**Example:**  
To block HTTP traffic, you would block TCP traffic from source port 80 (the default HTTP port). To allow HTTPS traffic, you would allow TCP traffic from source port 443 (the default HTTPS port). However, this approach is not efficient, as services can run on non-default ports (e.g., HTTP on port 53 or 6667).

Visit the [Service Name and Transport Protocol Port Number Registry](https://www.iana.org/assignments/service-names-port-numbers/service-names-port-numbers.xhtml) to learn more about default port numbers and answer the following questions.