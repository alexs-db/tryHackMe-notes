# Next-Generation Firewalls (NGFW)

Traditional firewalls, such as packet-filtering firewalls, rely on port numbers to identify protocols and applications. To block an application, you would typically block its associated port. However, this approach is no longer effective, as many applications disguise themselves by using ports assigned to other services. Additionally, the widespread use of encryption (e.g., SSL/TLS) further complicates traffic identification. As a result, port numbers alone are no longer a reliable method for identifying applications.

## Capabilities of Next-Generation Firewalls

Next-Generation Firewalls are designed to address the challenges faced by modern enterprises. Key capabilities include:

- **Integrated Intrusion Prevention System (IPS):** NGFWs combine traditional firewall functions with real-time IPS to detect and stop threats immediately.
- **User and Traffic Identification:** They can enforce security policies on a per-user or per-group basis by identifying users and their traffic.
- **Application and Protocol Identification:** NGFWs can recognize applications and protocols regardless of the port numbers being used.
- **Content Inspection:** They inspect transmitted content and enforce security policies if any violating content is detected.
- **Decryption of Encrypted Traffic:** NGFWs can decrypt SSL/TLS and SSH traffic, restricting evasive techniques that use encryption to transfer malicious files.

A properly configured and deployed NGFW can render many attacks ineffective.