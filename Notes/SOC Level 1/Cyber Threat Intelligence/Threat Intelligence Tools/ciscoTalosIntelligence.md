# Cisco Talos Intelligence

IT and cybersecurity companies collect massive amounts of information for threat analysis and intelligence. Cisco, as one of these companies, has assembled a large team of security practitioners called **Cisco Talos**. This team provides actionable intelligence, visibility on indicators, and protection against emerging threats using data collected from Cisco products. The solution is accessible as **Talos Intelligence**.

## Cisco Talos Teams

Cisco Talos encompasses six key teams:

- **Threat Intelligence & Interdiction**: Correlates and tracks threats, turning simple IOCs into context-rich intelligence.
- **Detection Research**: Analyzes vulnerabilities and malware to create rules and content for threat detection.
- **Engineering & Development**: Maintains and updates inspection engines to identify and triage emerging threats.
- **Vulnerability Research & Discovery**: Collaborates with vendors to identify and report security vulnerabilities.
- **Communities**: Maintains the team's image and open-source solutions.
- **Global Outreach**: Shares intelligence with customers and the security community through publications.

More information about Cisco Talos can be found in their [White Paper](https://www.cisco.com/c/en/us/about/security-center/talos.html).

---

## Talos Dashboard

When accessing the open-source solution, users are presented with a reputation lookup dashboard featuring a world map. This map provides an overview of email traffic, indicating whether emails are legitimate, spam, or malware across various countries. Clicking on a marker reveals more information about associated IP and hostname addresses, daily volume, and type.

### Dashboard Tabs

At the top, several tabs provide different types of intelligence resources. The primary tabs for analysts include:

- **Vulnerability Information**: Reports on disclosed and zero-day vulnerabilities, marked with CVE numbers and CVSS scores. Selecting a report provides details, including the timeline for publication. Microsoft vulnerability advisories and applicable Snort rules are also available.
- **Reputation Center**: Searchable threat data related to IPs and files using SHA256 hashes. Analysts use these options for investigations. Additional email and spam data can be found under the **Email & Spam Data** tab.

---

## Task

Use the information gathered from inspecting the `Email1.eml` file from Task 5 to answer the following questions using Cisco Talos Intelligence. Note: The VM launched in Task 5 does not have Internet access.