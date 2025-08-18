# Introduction to MISP

**MISP** (Malware Information Sharing Platform) is an open-source threat intelligence platform designed to facilitate the collection, storage, and distribution of threat intelligence and Indicators of Compromise (IOCs) related to malware, cyber attacks, financial fraud, or any intelligence within a trusted community.

Information sharing in MISP follows a distributed model, supporting closed, semi-private, and open (public) communities. Threat information can be distributed and consumed by Network Intrusion Detection Systems (NIDS), log analysis tools, and Security Information and Event Management Systems (SIEM).

---

## MISP Use Cases

- **Malware Reverse Engineering:** Share malware indicators to understand malware families.
- **Security Investigations:** Search, validate, and use indicators for breach investigations.
- **Intelligence Analysis:** Gather information about adversary groups and their capabilities.
- **Law Enforcement:** Use indicators to support forensic investigations.
- **Risk Analysis:** Research new threats, their likelihood, and occurrences.
- **Fraud Analysis:** Share financial indicators to detect fraud.

---

## Core Functionalities of MISP

- **IOC Database:** Store technical and non-technical information about malware samples, incidents, attackers, and intelligence.
- **Automatic Correlation:** Identify relationships between attributes and indicators from malware, attack campaigns, or analysis.
- **Data Sharing:** Share information using different distribution models and among different MISP instances.
- **Import & Export Features:** Import and export events in various formats to integrate with systems such as NIDS, HIDS, and OpenIOC.
- **Event Graph:** Visualize relationships between objects and attributes identified from events.
- **API Support:** Integrate with other systems to fetch and export events and intelligence.

---

## Key Terminologies in MISP

- **Events:** Collections of contextually linked information.
- **Attributes:** Individual data points associated with an event (e.g., network or system indicators).
- **Objects:** Custom compositions of attributes.
- **Object References:** Relationships between different objects.
- **Sightings:** Time-specific occurrences of a data point or attribute, providing credibility.
- **Tags:** Labels attached to events or attributes.
- **Taxonomies:** Classification libraries used to tag, classify, and organize information.
- **Galaxies:** Knowledge base items used to label events or attributes.
- **Indicators:** Information that can detect suspicious or malicious cyber activity.