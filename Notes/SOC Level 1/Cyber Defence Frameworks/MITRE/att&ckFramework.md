# What is the ATT&CK® Framework?

According to the [MITRE ATT&CK® website](https://attack.mitre.org/), "MITRE ATT&CK® is a globally-accessible knowledge base of adversary tactics and techniques based on real-world observations." In 2013, MITRE began addressing the need to record and document common TTPs (Tactics, Techniques, and Procedures) used by APT (Advanced Persistent Threat) groups against enterprise Windows networks. This effort started as an internal project known as FMX (Fort Meade Experiment), where selected security professionals emulated adversarial TTPs against a network, and data was collected from these simulated attacks. The gathered data formed the foundation of what we know today as the ATT&CK® framework.

The ATT&CK® framework has grown and expanded over the years. Initially focused solely on the Windows platform, it now covers other platforms such as macOS and Linux. The framework is continuously updated with contributions from security researchers and threat intelligence reports. It is a valuable resource for both blue teamers (defenders) and red teamers (attackers).

If you haven't already, visit the [ATT&CK® website](https://attack.mitre.org/).

---

## ATT&CK® Matrix for Enterprise

At the bottom of the page, you'll find the **ATT&CK® Matrix for Enterprise**. Across the top of the matrix are 14 categories, each representing a tactic. Each category contains techniques adversaries may use to achieve that tactic. The categories align with the seven-stage Cyber Attack Lifecycle (credit: Lockheed Martin's Cyber Kill Chain).

> **ATT&CK Matrix v11.2**

For example, under **Initial Access**, there are 9 techniques. Some techniques, like Phishing, have sub-techniques.

Clicking the gray bar to the right of a technique reveals its sub-techniques.

To better understand a technique and its sub-techniques, click on **Phishing**. This will take you to a dedicated page with a description, procedure examples, and mitigations for the technique.

You can also use the **Search** feature to find information about any technique, sub-technique, or group.

---

## MITRE ATT&CK® Navigator

The same data can be viewed using the [MITRE ATT&CK® Navigator](https://mitre-attack.github.io/attack-navigator/):

> "The ATT&CK® Navigator is designed to provide basic navigation and annotation of ATT&CK® matrices, something that people are already doing today in tools like Excel. We've designed it to be simple and generic—you can use the Navigator to visualize your defensive coverage, your red/blue team planning, the frequency of detected techniques, or anything else you want to do."

You can access the Navigator view from a group or tool page by clicking the **ATT&CK® Navigator Layers** button.

In the sub-menu, select **view**.

Let's get acquainted with this tool. Click [here](https://attack.mitre.org/groups/G0008/) to view the ATT&CK® Navigator for Carbanak.

At the top left, there are three sets of controls:

- **Selection controls**
- **Layer controls**
- **Technique controls**

Explore each option to become familiar with them. The question mark at the far right provides additional information about the navigator.

---

## Summary

- The ATT&CK Matrix helps map threat groups to their tactics and techniques.
- There are various ways to search and explore the framework.
- Start by answering the questions from the [Phishing page](https://attack.mitre.org/techniques/T1566/). (Note: This link is for version 8 of the ATT&CK Matrix.)

---