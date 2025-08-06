## The Role of Cyber Threat Intelligence (CTI) in the SOC

A junior analyst logs in for their shift and finds two hundred fresh alerts waiting—everything from benign network scans to a PowerShell beacon that feels out of place. The ticket queue does not politely pace itself; it demands quick, confident decisions. This is where CTI earns its keep.

Threat intelligence provides the context that helps an analyst decide which of those two hundred alerts represents genuine danger. With reliable context, the SOC spends energy on the right issues and sleeps better when the dashboards are finally calm.

In concrete terms, CTI seeks to answer three essential questions:

1. **Who, or what, is on the other end of this alert indicator?**
2. **What was their behaviour in the past?**
3. **How does my organisation respond, and what should I do about it right now?**

When these questions are addressed early—preferably within minutes—an L1 analyst buys precious time downstream for the incident‑response (IR) team. CTI is therefore not the exclusive domain of intel specialists; it is a front‑line tool that elevates SOC triage from guesswork to calculated action.

---

## From Raw Data to Usable Intelligence

Information security literature distinguishes **data**, **information**, and **intelligence**, yet the three terms often blur in daily conversation. Making them explicit clarifies an analyst's objective.

| Layer        | Definition                                 | Alert-queue example                        | SOC L1 action                |
|--------------|--------------------------------------------|--------------------------------------------|------------------------------|
| Data         | An unprocessed observable                  | `45.155.205.3 :443`                        | Capture the artefact         |
| Information  | Data plus factual annotation               | IP registered to Hetzner, first seen 2023-07-14 | Record attributes            |
| Intelligence | Analysed information that answers "so-what"| IP belongs to the current BumbleBee C2; block immediately | Escalate or suppress         |

A Level 1 analyst is responsible for making the artefacts usable and enriching them until they qualify as intelligence, or demonstrating that they never will. That push is enacted through **enrichment**: rapid, methodical lookups of public, commercial, and internal sources that shed light on origin, behaviour, and relevance.

During the ascent of data to intelligence, three more labels become paramount for analysts to know:

- **Indicator of Compromise (IOC):** Evidence of a breach, such as a C2 address in the logs.
- **Indicator of Attack (IOA):** A malicious action, such as PowerShell launching an unknown service, is underway.
- **Tactics, Techniques, and Procedures (TTP):** An adversary's detailed methodologies expressed in MITRE ATT&CK IDs and descriptions.

---

## Indicator Types Essential to First-Line Triage

Every artefact demands a tailored enrichment path. Memorising tools is less important than recognising what kind of indicator the alert supplies and knowing where to look.

| Indicator      | Example                           | First Resources                                                                 | Associated IOA or TTP Examples                      |
|----------------|-----------------------------------|---------------------------------------------------------------------------------|-----------------------------------------------------|
| IPv4 / IPv6    | `45.155.205.3`                    | WHOIS (ASN, allocation date), VirusTotal Relations, Shodan banner scan          | IOA: Repeated SSH failures<br>TTP: T1110.003 Password Guessing |
| Domain / FQDN  | `malicious-updates[.]net`         | WHOIS age, RiskIQ/SecurityTrails passive-DNS, urlscan.io                        | IOA: Surge of DNS queries to a 24-hour-old domain    |
| URL            | `hxxp://malicious-updates[.]net/login` | URLhaus reputation, urlscan.io behaviour graph, Any.Run dynamic run (network off) | IOA: Browser POST to `/gateway.php` with payload     |
| File hash      | `e99a18c428cb38d5…`               | VirusTotal static & dynamic, Hybrid-Analysis, MalShare corpus                   | TTP: T1055 Process Injection into regsvr32.exe       |
| E-mail address | `billing@evil-corp.com`           | MXToolbox header analysis, Have I Been Pwned                                    | IOA: SPF failure plus recent domain registration     |
| Local artefact | `HKCU\Software\Run\updater.exe`   | Sigma rules, EDR prevalence query, Vendor knowledge base                        | TTP: T1060.001 Registry Run Keys                    |

> **Tip:** Maintain a browser bookmark folder or a SIEM launcher panel that opens your preferred look-ups with the highlighted indicator pre-populated. The thirty seconds saved per alert compound into hours over a month.

---

## Feeds, Platforms, and Why the Distinction Matters

Most SOCs do not build or have intelligence in-house. The insights must be integrated and ingested from reliable sources.

- **Feed:** A scheduled stream of indicators, usually delivered in formats such as CSV, JSON, STIX, or through TAXII. Over-ingesting feeds without curation drowns analysts in false positives and erodes trust in the programme.
- **Platform:** A structured repository that stores indicators, tracks enrichment, maps relationships, and enforces sharing permissions. MISP and OpenCTI are leading open-source examples.

Sound CTI practice introduces feeds gradually, confirms they align with the organisation's threat model, and promotes them into the platform only after measuring actionability. The platform then becomes the single source of truth—an analyst queries it first, ensuring biographies of indicators evolve rather than fork.

---

## Sources of Cyber Threat Intelligence

Intelligence is only as trustworthy as its source, for it drives credibility and steers legal review if an IOC later triggers business disruption. As an analyst, you must know and note where each indicator originated.

There are four broad sources that you will come across in your practice:

1. **Internal telemetry:** SIEM logs, EDR detections, phishing-mailbox submissions provide the highest immediate relevance.
2. **Commercial services:** Vendor premium feeds, paid sandboxes, and closed-source analytics. These provide high fidelity, but may have export and sharing limits based on licensing.
3. **Open-source intelligence (OSINT):** AbuseIPDB, URLhaus, public blogs with IOCs, and academic research. Before applying, information from these sources will need to be cross-confirmed.
4. **Communities & ISACs:** Sector-specific lists marked with labels and rich context (e.g., FS-ISAC).

---

## Threat Intelligence Classifications

Threat intelligence is geared towards understanding the relationship between your operational environment and your adversary. With this in mind, we can break down threat intel into the following classifications:

- **Strategic intel:** High-level intelligence that looks into the organisation's threat landscape and maps out the risk areas based on trends, patterns and emerging threats that may impact business decisions.  
    _Example: An annual ransomware trends report predicting a shift to data-wiping extortion in healthcare._

- **Tactical intel:** Assessments of adversaries' behaviours through analysis of tactics, techniques, and procedures (TTPs).  
    _Example: Advisory notes detailing new T1059.005 (Visual Basic) abuse in malspam._

- **Operational intel:** Campaign-specific details about the motives and intent to perform an attack. This is useful for understanding the critical assets available in the organisation (people, processes, and technologies) that may be targeted.

- **Technical intel:** Atomic indicators and artefacts such as IPs and hashes related to an attack.

L1 analysts will escalate many Technical IOCs, observe and document Tactical IOAs, and identify patterns that feed Operational reporting.