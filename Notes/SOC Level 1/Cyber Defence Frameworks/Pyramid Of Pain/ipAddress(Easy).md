# IP Address (Easy)

You may have learned the importance of an IP address from the "What is Networking?" room. An IP address is used to identify any device connected to a network, including desktops, servers, and even CCTV cameras. We rely on IP addresses to send and receive information over the network. However, in this context, we will focus on how IP addresses are used as indicators within the Pyramid of Pain, rather than their structure and functionality.

## IP Addresses in the Pyramid of Pain

In the Pyramid of Pain, IP addresses are represented with the color **green**. You might wonder why this is the case and what the green color signifies.

From a defense standpoint, knowing the IP addresses an adversary uses can be valuable. A common defense tactic is to block, drop, or deny inbound requests from specific IP addresses at your perimeter or external firewall. However, this tactic is not foolproof, as experienced adversaries can easily bypass it by switching to a new public IP address.

> **Note:** Do not attempt to interact with any malicious IP addresses you may encounter in threat intelligence feeds or online resources.

## Fast Flux Technique

One way adversaries can evade IP blocking is by using a technique called **Fast Flux**.

According to Akamai, Fast Flux is a DNS technique used by botnets to hide phishing, web proxying, malware delivery, and malware communication activities behind compromised hosts acting as proxies. The purpose of using a Fast Flux network is to make communication between malware and its command and control server (C&C) difficult to discover by security professionals.

The primary concept of a Fast Flux network is to associate multiple, constantly changing IP addresses with a single domain name.

For a detailed fictional scenario explaining Fast Flux, see Palo Alto's article:  
*["Fast Flux 101: How Cybercriminals Improve the Resilience of Their Infrastructure to Evade Detection and Law Enforcement Takedowns"](https://unit42.paloaltonetworks.com/fast-flux-101/)*
