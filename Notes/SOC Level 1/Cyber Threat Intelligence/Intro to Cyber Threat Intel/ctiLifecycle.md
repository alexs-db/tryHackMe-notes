# Cyber Threat Intelligence Lifecycle

Cyber Threat Intelligence (CTI) follows a six-phase intelligence lifecycle that transforms raw data into contextualised, action-oriented insights for triaging security incidents. We'll explore this lifecycle through the narrative of a SOC Level 1 analyst, Alex, as they protect TryHatMe's production database.

---

## The CTI Lifecycle Loop

Before diving into the lifecycle, it's helpful to understand some key concepts.

### Traffic Light Protocol (TLP): A Primer

The Traffic Light Protocol is a four-colour labelling scheme defined by FIRST.org that governs how widely intelligence may be shared:

| TLP Label   | Sharing Boundary                                      | Typical SOC L1 Behaviour                                         |
|-------------|------------------------------------------------------|------------------------------------------------------------------|
| TLP: CLEAR  | No restriction                                       | Post to the internal wiki or platform.                           |
| TLP: GREEN  | Share with peer community but not publicly           | Upload to MISP/Slack workspace restricted to partner SOCs.       |
| TLP: AMBER  | Organisation-wide, external sharing need-to-know     | Keep within company CTI platform; reference, do not copy, in tickets. |
| TLP: RED    | Named recipients only                                | Store in encrypted note; do not post to ticketing system without clearance. |

> **Note:** When investigating breaches, a Level 1 analyst ensures that an IOC's TLP label—if provided—travels with the indicator into any platform or triage note. Violating the label can breach contractual obligations and erode trust with intelligence partners.

### Intel Formats

Analysts encounter intelligence in various formats. Once gathered, they must share and update other analysts on new findings. One common format is **Structured Threat Information Expression (STIX)**, a JSON schema for describing threat indicators, relationships, and context in a machine-readable form.

---

## Scenario Premise

TryHatMe Corp holds sensitive customer data in a PostgreSQL server on a segmented subnet, protected by a next-generation firewall (NGFW) and monitored by an Endpoint Detection and Response (EDR) agent. Senior management tasks the SOC with integrating cyber-threat intelligence to enhance controls. Alex, the morning-shift L1 analyst, is assigned to build and exercise this workflow.

---

## Step 1: Direction – Defining the Mission

Alex, the CTI lead, and the database administrator translate the mandate into an intelligence requirement:

- **Primary asset:** PostgreSQL production database
- **Business risk:** Data breach fines (GDPR), loss of customer trust
- **Available controls:** NGFW (IP/domain blocking), EDR (file hash quarantine)
- **Initial CTI goal:** Use threat-feed indicators to:
    - Block or alert on suspicious IPs at the firewall
    - Detect known malicious file hashes at the EDR layer

**Key Questions:**
- **Q1:** Which external IPs/domains are currently used to exploit PostgreSQL services or exfiltrate records?
- **Q2:** Which malware families targeting PostgreSQL drivers/credentials are active this week, and what are their file hashes?

---

## Step 2: Collection – Assembling the Raw Material

Alex gathers intelligence from four sources:

| Source                                      | Rationale                                   | Example Artefacts Collected                                 |
|----------------------------------------------|---------------------------------------------|-------------------------------------------------------------|
| Commercial feed from NGFW vendor             | Tailored, high-fidelity                     | 37 IPv4 addresses flagged as database-exfil C2 (last 24h)   |
| AbuseIPDB (tag: PostgreSQL-brute-force)      | Rapid, community-driven updates             | 15 IPs, 4 domains                                           |
| Internal MISP                               | Historical view of incidents                | 2 SHA-256 hashes of PgSQL credential stealers               |
| Vendor threat report (this week)             | Strategic insight, technical IOCs           | 1 new malware hash, 3 C2 domains                            |

Alex exports each feed in STIX or CSV, storing a dated copy in the SOC's "raw-intel" S3 bucket for reproducibility.

---

## Step 3: Processing – Normalising and Correlating the Data

Raw feeds rarely align perfectly. Processing involves:

- **Normalising**: Standardising formats (e.g., IPv6 compression, lowercase domains, stripping subnet masks)
- **Correlating**: Deduplicating against existing indicators, tagging with source, date, and TLP label
- **Output**: Creating action files such as:
    - `firewall_blocklist.csv` (indicator, action, comments)
    - `edr_hash_rules.yar` (YARA rules for hash blocking)

> **Example:** If an IP appears in two feeds with different TLPs (e.g., TLP: AMBER and TLP: CLEAR), the stricter TLP prevails (TLP: AMBER).

---

## Step 4: Analysis – Turning Information Into Judgement

Blocking every suspicious indicator risks false positives. Alex evaluates relevance and matches intel to the investigation:

- **Firewall indicators (Q1):** Splunk query shows one NGFW IP attempted but failed to access TCP 5432 on the production subnet—validating the indicator.
- **Hash indicators (Q2):** OpenCTI links a new hash to PgSteal malware; sandbox analysis confirms credential-dump behaviour. The hash is marked high priority due to relevance.

**Indicator Grading:**

| Confidence | Source Agreement      | Local Sightings      | Action           |
|------------|----------------------|----------------------|------------------|
| High       | ≥2 sources           | ≥1 local attempt     | Immediate block  |
| Medium     | Single trusted source| No local hits        | Alert-only       |
| Low        | OSINT only           | No context           | Monitor 14 days  |

Seven IPs and one hash meet the high bar; others are queued as medium and monitored.

---

## Step 5: Dissemination – Getting Intelligence to the Right Consumers

Alex delivers tailored outputs:

| Stakeholder      | Format                        | Purpose                                               |
|------------------|------------------------------|-------------------------------------------------------|
| Firewall team    | CSV upload + change-ticket    | Own block rules; ticket documents risk and TLP        |
| Endpoint team    | YARA rule set in EDR console  | Load rules into policy                                |
| CTI platform     | Indicator objects with tags   | Maintain artefact history, support future correlations|
| Management       | 200-word summary in memo      | Show ROI for the process                              |

Each consumer receives only the detail required to act.

---

## Step 6: Feedback – Measuring and Refining the Cycle

Two weeks later, metrics show progress:

| KPI                                 | Baseline | After First Cycle         |
|--------------------------------------|----------|--------------------------|
| Median dwell time of PgSQL brute-force IPs | 48 h    | 0 h (pre-emptive block)  |
| False-positive rate on new blocks    | —        | 0% (none revoked)        |

With objectives met, management approves expansion (e.g., DNS tunnelling IOCs). Alex updates the direction document, closes the feedback loop, and schedules the next iteration.
