# Security Operations Center (SOC) Overview

The core function of a SOC (Security Operations Center) is to **investigate, monitor, prevent, and respond to threats** in the cyber realm 24/7. According to Trellix:

> "Security operations teams are charged with monitoring and protecting many assets. These include intellectual property, personnel data, business systems, and brand integrity."

As the implementation component of an organisation's overall cybersecurity framework, security operations teams act as the central point of collaboration in coordinated efforts to monitor, assess, and defend against cyberattacks. The number of people working in the SOC can vary depending on the organisation's size.

---

## SOC Responsibilities

Security analysts in a SOC are exposed to a variety of responsibilities, including:

### 1. Preparation and Prevention

- Stay informed about current cybersecurity threats (resources like Twitter and Feedly can help).
- Detect and hunt threats.
- Develop and maintain a security roadmap to protect the organisation.
- Prepare for worst-case scenarios.

**Prevention methods include:**
- Gathering intelligence on the latest threats, threat actors, and their TTPs (Tactics, Techniques, and Procedures).
- Maintaining security controls (e.g., updating firewall signatures, patching vulnerabilities).
- Block-listing and safe-listing applications, email addresses, and IPs.

> To better understand TTPs, refer to [CISA's alert on APT40](https://us-cert.cisa.gov/ncas/alerts/aa21-200a).

---

### 2. Monitoring and Investigation

- Use SIEM (Security Information and Event Management) and EDR (Endpoint Detection and Response) tools to monitor for suspicious or malicious activity.
- Prioritise alerts based on severity: **Low, Medium, High, Critical** (always start with Critical).
- Properly configured monitoring tools are essential for effective threat mitigation.

**Junior Security Analysts:**
- Play a key role in triaging alerts.
- Investigate how attacks work and prevent further damage.
- Ask critical questions: *How? When? Why?*
- Drill down into data logs and alerts, often using open-source tools.

---

### 3. Response

- Coordinate and take action on compromised hosts.
- Actions may include isolating hosts, terminating malicious processes, deleting malicious files, and more.
