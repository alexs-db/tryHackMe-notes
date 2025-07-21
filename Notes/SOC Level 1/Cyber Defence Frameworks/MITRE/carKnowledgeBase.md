# Cyber Analytics Repository (CAR)

The **MITRE Cyber Analytics Repository (CAR)** is a knowledge base of analytics developed by MITRE, based on the [MITRE ATT&CK®](https://attack.mitre.org/) adversary model. CAR defines a data model used in its pseudocode representations and includes implementations targeted at specific tools (e.g., Splunk, EQL). CAR focuses on providing validated and well-explained analytics, especially regarding their operating theory and rationale.

---

Instead of further explanation, let's explore CAR in practice. With knowledge from the previous section, you should be able to understand the information CAR provides.

## Example: CAR-2020-09-001 — Scheduled Task - File Access

Visiting this analytic's page, you'll find:

- A brief description of the analytic
- References to ATT&CK (technique, sub-technique, and tactic)
- Pseudocode and a Splunk query for searching this analytic
- Reference to Sysmon (If unfamiliar, check out the [Sysmon room](https://tryhackme.com/room/sysmon))

---

## Exploring CAR

To take full advantage of CAR, you can view:

- [Full Analytic List](https://car.mitre.org/analytics/)
- [CAR ATT&CK® Navigator layer](https://car.mitre.org/attack-navigator/)

### Full Analytic List

This view shows available implementations for each analytic and the applicable OS platform.

### CAR ATT&CK Navigator

Techniques highlighted in purple represent analytics currently in CAR.

---

## Another Example: CAR-2014-11-004 — Remote PowerShell Sessions

This analytic provides:

- Pseudocode
- An EQL (Event Query Language) version

EQL can be used to query, parse, and organize Sysmon event data. [Learn more about EQL here.](https://www.elastic.co/guide/en/security/current/eql.html)

---

## Summary

CAR is a valuable resource for finding analytics that go beyond the Mitigation and Detection summaries in the ATT&CK® framework. It is not a replacement for ATT&CK®, but an additional tool for defenders.
