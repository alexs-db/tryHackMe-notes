# Feeds

Feeds are resources containing indicators that can be imported into MISP, providing attributed information about security events. They offer analysts and organisations continuously updated threat intelligence to aid in proactive defence.

**MISP Feeds allow you to:**
- Exchange threat information
- Preview events with associated attributes and objects
- Select and import events to your instance
- Correlate attributes between events and feeds

Feeds are enabled and managed by the Site Admin for analysts to obtain event and indicator information.

---

# Taxonomies

A taxonomy classifies information based on standard features or attributes. In MISP, taxonomies categorise events, indicators, and threat actors using tags.

**Analysts use taxonomies to:**
- Set events for further processing by external tools (e.g., VirusTotal)
- Ensure events are classified before publication by the Organisation Admin
- Enrich intrusion detection system exports with relevant tags

Taxonomies are expressed as machine tags, which include:
- **Namespace:** Defines the tag's property
- **Predicate:** Specifies the property attached to the data
- **Value:** Numerical or text details mapping the property

> *(Source: MISP)*

Taxonomies are listed under the Event Actions tab. The Site Admin can enable relevant taxonomies.

---

# Tagging

Tags from feeds and taxonomies can be placed on events and attributes to identify them based on indicators or threats. Tagging enables effective sharing of threat information among users, communities, and organisations using MISP.

For example, in a CobaltStrike event, tags can be added by clicking buttons in the Tags section and searching for appropriate options. Buttons represent global and local tags. Analysts or organisations can also add unique tags to their MISP instance for efficient information management and sharing.

---

## Tagging Best Practices

### Event Level vs Attribute Level

Tags can be added to both events and attributes, and are inheritable. It is recommended to set tags at the event level, adding tags to attributes only when exceptions apply. This enables more fine-grained analysis.

### Minimal Subset of Tags

Consider these essential tags for well-defined event distribution:
- **Traffic Light Protocol:** Colour schema guiding intelligence sharing
- **Confidence:** Indicates data quality and vetting status
- **Origin:** Describes the information source (automation or manual investigation)
- **Permissible Actions Protocol:** Advanced classification indicating how data can be used for compromise searches within the organisation
