# What is Data Exfiltration

Data Exfiltration is the process of taking an unauthorized copy of sensitive data and moving it from the inside of an organization's network to the outside. It is important to note that Data Exfiltration is a post-compromise process where a threat actor has already gained access to a network and performed various activities to get hands on sensitive data. Data Exfiltration often happens at the last stage of the Cyber Kill Chain model, **Actions on Objectives**.

## Cyber Kill Chain

Data exfiltration is also used to hide an adversary's malicious activities and bypass security products. For example, the DNS exfiltration technique can evade security products, such as a firewall.

Sensitive data can be in various types and forms, and it may include the following:

- Usernames and passwords or any authentication information.
- Bank account details.
- Business strategic decisions.
- Cryptographic keys.
- Employee and personnel information.
- Project code data.

## How to Use Data Exfiltration

There are three primary use case scenarios of data exfiltration:

1. Exfiltrate data.
2. Command and control (C2) communications.
3. Tunneling.

### Traditional Data Exfiltration

The traditional Data Exfiltration scenario involves moving sensitive data out of the organization's network. An attacker can make one or more network requests to transfer the data, depending on the data size and the protocol used. Note that a threat actor does not care about the reply or response to their request. Thus, all traffic will be in one direction, from inside the network to outside. Once the data is stored on the attacker's server, they log into it and retrieve the data.

### C2 Communications

Many C2 frameworks provide options to establish a communication channel, including standard and non-traditional protocols to send commands and receive responses from a victim machine. In C2 communications, a limited number of requests are made where an attacker sends a request to execute a command on the victim's machine. Then, the agent's client executes the command and sends a reply with the result over a non-traditional protocol. The communications will go in two directions: into and out of the network.

### Tunneling

In the Tunneling scenario, an attacker uses this data exfiltration technique to establish a communication channel between a victim and an attacker's machine. The communication channel acts as a bridge to let the attacker's machine access the entire internal network. There will be continuous traffic sent and received while establishing the connection.

## Upcoming Topics

In the coming tasks, we will discuss the following techniques and use cases:

- Exfiltrate using TCP socket and Base64.
- Exfiltrate using SSH.
- Exfiltrate using HTTPS (POST request).
- ICMP.
- DNS.