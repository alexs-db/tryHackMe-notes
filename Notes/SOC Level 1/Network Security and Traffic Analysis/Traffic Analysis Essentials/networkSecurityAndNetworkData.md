# Network Security

Network security focuses on two core concepts: **authentication** and **authorisation**. Various tools, technologies, and approaches are used to implement and measure these concepts, ensuring continuity and reliability. Network security operations are structured around three base control levels:

## Base Network Security Control Levels

- **Physical**  
    Prevents unauthorised physical access to networking devices, cable boards, locks, and related components.

- **Technical**  
    Prevents unauthorised access to network data through tunnels, security layers, and other measures.

- **Administrative**  
    Ensures consistency in security operations via policies, access levels, and authentication processes.

---

## Main Approaches

| Access Control | Threat Control |
|:--------------:|:--------------:|
| Ensures authentication and authorisation. | Detects and prevents anomalous/malicious activities on the network, including both internal and external traffic probes. |

---

## Key Elements of Access Control

- **Firewall Protection**  
    Controls network traffic with security rules, blocking suspicious/malicious traffic while allowing legitimate traffic.

- **Network Access Control (NAC)**  
    Verifies device compliance before allowing network access.

- **Identity and Access Management (IAM)**  
    Manages asset identities and user access to data systems and resources.

- **Load Balancing**  
    Distributes tasks across resources to improve data processing flow.

- **Network Segmentation**  
    Isolates access levels and groups assets to protect sensitive devices/data.

- **Virtual Private Networks (VPN)**  
    Enables encrypted communication between devices, typically for secure remote access.

- **Zero Trust Model**  
    Configures access and permissions at a minimum level: "Never trust, always verify".

---

## Key Elements of Threat Control

- **Intrusion Detection and Prevention (IDS/IPS)**  
    Inspects traffic, creates alerts (IDS), or resets connections (IPS) upon detecting threats.

- **Data Loss Prevention (DLP)**  
    Inspects traffic and blocks extraction of sensitive data.

- **Endpoint Protection**  
    Protects endpoints using encryption, antivirus, antimalware, DLP, and IDS/IPS.

- **Cloud Security**  
    Protects cloud resources from threats and data leakage using VPNs and encryption.

- **Security Information and Event Management (SIEM)**  
    Detects threats and manages incidents using event and context analysis.

- **Security Orchestration Automation and Response (SOAR)**  
    Coordinates and automates security tasks, supporting vulnerability management and incident response.

- **Network Traffic Analysis & Network Detection and Response**  
    Inspects network traffic to identify anomalies and threats.

---

## Typical Network Security Management Operations

| Deployment | Configuration | Management | Monitoring | Maintenance |
|------------|---------------|------------|------------|-------------|
| Device/software installation | Initial configuration | Automation | System/user activity monitoring | Upgrades/security updates |
| Feature configuration | Network access configuration | Security policy implementation | Threat/log monitoring | Rule/configuration updates |
| NAT/VPN implementation | Threat mitigation | Licence management | Traffic sample capturing | Licence management |

---

## Managed Security Services (MSS)

Not all organisations have dedicated security teams due to budget, skillset, or size. **Managed Security Services (MSS)** are outsourced services provided by **Managed Security Service Providers (MSSPs)**. MSS are cost-effective, easy to engage, and simplify management. Common MSS elements include:

- **Network Penetration Testing**  
    Simulates attacker techniques to assess network security.

- **Vulnerability Assessment**  
    Discovers and analyses vulnerabilities in the environment.

- **Incident Response**  
    Organised approach to identifying, containing, and eliminating security incidents.

- **Behavioural Analysis**  
    Analyses system/user behaviours to detect anomalies, threats, and attacks.
