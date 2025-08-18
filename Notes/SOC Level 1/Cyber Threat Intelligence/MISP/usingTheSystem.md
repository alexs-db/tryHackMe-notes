# Dashboard

The analyst's view of MISP provides functionalities to track, share, and correlate events and IOCs identified during investigations. The dashboard menu includes:

- **Home button**: Returns to the application's start screen, event index page, or a custom home page.
- **Event Actions**: Access to creation, modification, deletion, publishing, searching, and listing of events and attributes.
- **Dashboard**: Create a custom dashboard using widgets.
- **Galaxies**: Shortcut to the list of MISP Galaxies. More details in the Feeds & Taxonomies section.
- **Input Filters**: View and manage input validation, regular expression replacements, and blocklists.
- **Global Actions**: View and edit your profile, access the manual, news, terms of use, active organisations, and contribution histograms.
- **MISP**: Link to your base URL.
- **Name**: Auto-generated from the mail address of the currently logged-in user.
- **Envelope**: User Dashboard for notifications and recent changes.
- **Log out**: End your session immediately.

---

## Home Dashboard

Displays available events.

---

# Event Management

The **Event Actions** tab is where analysts create malware investigation correlations by providing descriptions and attributes. The process includes three main phases:

1. **Event Creation**
2. **Populating events with attributes and attachments**
3. **Publishing**

### Event Creation

Events store general information about an incident or investigation. Add a description, time, and risk level using the **Add Event** button. Specify the distribution level:

- **Your organisation only**: Only members of your organisation can see the event.
- **This Community-only**: Members of your MISP community, including organisations on this server and synchronised servers, can see the event.
- **Connected communities**: Includes all organisations on this server, synchronised servers, and hosting organisations two hops away.
- **All communities**: Shares the event with all MISP communities.

You can also add a sharing group to define a list of organisations for event sharing.

Event details can be populated using predefined templates and attributes. For example, use the Phishing E-mail category for a CobaltStrike investigation.

---

### Attributes & Attachments

Attributes can be added manually or imported (e.g., OpenIOC, ThreatConnect). To add manually, click **Add Attribute** and fill out the form.

Key options:

- **Intrusion Detection System**: Use the attribute as an IDS signature for NIDS data export.
- **Batch import**: Enter multiple attributes (e.g., IP addresses) separated by line breaks for bulk entry.

Example: Add an Emotet Epoch 4 C2 IP address from an IOC text file.

Attachments (malware samples, reports, artefacts) can also be added. Mark files as malware to ensure they are zipped and password-protected.

---

### Publish Event

After event creation, the organisation admin reviews and publishes events, sharing them according to the selected distribution channels.