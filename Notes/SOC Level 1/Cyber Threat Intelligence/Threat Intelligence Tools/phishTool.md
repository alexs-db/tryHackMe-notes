# PhishTool Introduction

Before starting the task, click **Start Machine** to launch the attached VM and open it in Split View. You will use the same machine for tasks 7 and 8.

## Overview

This task introduces **PhishTool**, an email analysis tool for your cybersecurity toolkit. While you don't need to use PhishTool to complete this task, the principles covered will be helpful.

## Email Phishing

Email phishing is a common precursor to cyber attacks. Attackers trick users into opening malicious files or links by making emails appear legitimate. This can lead to malware infections, credential theft, financial fraud, or ransomware attacks.

For more information on phishing, check out these rooms:
- Phishing Emails 1
- Phishing Emails 2
- Phishing Emails 3
- Phishing Emails 4
- Phishing Emails 5

## About PhishTool

PhishTool elevates the perception of phishing as a serious threat and provides responsive email security. Through analysis, security analysts can uncover Indicators of Compromise (IOCs), prevent breaches, and generate forensic reports for containment and training.

PhishTool offers two versions:
- **Community** (focus of this task)
- **Enterprise**

Sign up for an account [here](https://phishtool.com). Note: The tool may be geo-blocked in some locations.

### Core Features

- **Email Analysis**: Retrieves metadata from phishing emails, explains actions, attachments, and URLs.
- **Heuristic Intelligence**: Integrates OSINT to help analysts understand attack techniques and social engineering methods.
- **Classification & Reporting**: Enables quick action and generates forensic records.

### Enterprise Features

- Manage user-reported phishing events.
- Report findings to users and keep them engaged.
- Integrate with Microsoft 365 and Google Workspace.

## Using PhishTool

After logging in, use the **Analysis** tab to upload emails for analysis (supported file formats). Other tabs include:
- **History**: Lists all submissions and their resolutions.
- **In-tray**: (Enterprise) Processes phish reports from team members via integrations.

### Analysis Tab Details

Once uploaded, you can review:
- **Headers**: Routing info (source/destination addresses, IPs, DNS, timestamp).
- **Received Lines**: Email traversal details across SMTP servers.
- **X-headers**: Extension headers for additional info.
- **Security**: SPF, DKIM, DMARC details.
- **Attachments**: Lists attached files.
- **Message URLs**: External URLs found in the email.

You can perform lookups and flag indicators as malicious. The right side displays the email's plaintext and source.

#### Resolution

Above the Plaintext section is the **Resolve** checkmark. Here, classify the email, flag artifacts, and set classification codes. Classified details appear on the **Resolution** tab.

---

## Scenario

You are a SOC Analyst tasked with analyzing a suspicious email, `Email1.eml`. Open the email using Thunderbird on the attached VM, analyze it, and answer the questions below.