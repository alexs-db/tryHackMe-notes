# Abuse.ch

Abuse.ch is a research project hosted by the Institute for Cybersecurity and Engineering at the Bern University of Applied Sciences in Switzerland. It was developed to identify and track malware and botnets through several operational platforms:

## Platforms

- **MalwareBazaar**: A resource for sharing malware samples.
- **Feodo Tracker**: Tracks botnet command and control (C2) infrastructure linked with Emotet, Dridex, and TrickBot.
- **SSL Blacklist**: Provides a blocklist for malicious SSL certificates and JA3/JA3s fingerprints.
- **URLHaus**: Shares malware distribution sites.
- **ThreatFox**: Shares indicators of compromise (IOCs).

Below is a brief overview of each platform:

---

### MalwareBazaar

MalwareBazaar is a comprehensive malware collection and analysis database. Key features include:

- **Malware Samples Upload**: Security analysts can upload malware samples for analysis via browser or API.
- **Malware Hunting**: Set up alerts to match tags, signatures, YARA rules, ClamAV signatures, and vendor detection.

*Dashboard: MalwareBazaar*

---

### Feodo Tracker

Feodo Tracker shares intelligence on botnet C&C servers associated with Dridex, Emotet (Heodo), TrickBot, QakBot, and BazarLoader/BazarBackdoor. Features:

- Database of C&C servers for investigation of suspicious IP addresses.
- IP and IOC blocklists, plus mitigation information to prevent botnet infections.

*Dashboard: Feodo Tracker*

---

### SSL Blacklist

SSL Blacklist identifies and detects malicious SSL connections. Features:

- Denylist of SSL certificates used by botnet C2 servers.
- Identification of JA3 fingerprints to help detect and block malware botnet C2 communications at the TCP layer.
- Browse or download SSL certificates and JA3 fingerprints for use in deny lists or threat hunting rulesets.

*Dashboard: SSL Blacklist*

---

### URLHaus

URLHaus focuses on sharing malicious URLs used for malware distribution. Features:

- Search database for domains, URLs, hashes, and filetypes suspected to be malicious.
- Generate feeds by country, AS number, or Top Level Domain for specific search needs.

*Dashboard: URLHaus*

---

### ThreatFox

ThreatFox enables security analysts to search for, share, and export IOCs associated with malware. Export formats include:

- MISP events
- Suricata IDS Ruleset
- Domain Host files
- DNS Response Policy Zone
- JSON files
- CSV files

---
