# OpenCTI Data Model

OpenCTI structures data primarily using the **Structured Threat Information Expression (STIX2)** standard. STIX is a serialized and standardized language format for threat intelligence exchange, enabling data to be represented as entities and relationships to trace the origin of information.

## Platform Architecture

The platform's architecture supports this data model. Below is an overview of the main architectural components:

- **GraphQL API**: Connects clients to the database and messaging system.
- **Write Workers**: Python processes that asynchronously write queries from the RabbitMQ messaging system.
- **Connectors**: Python processes for ingesting, enriching, or exporting data. Connectors integrate external systems and frameworks, helping users create threat intelligence relationships and improve defense tactics.

### Connector Classes

| Class                        | Description                                      | Examples                        |
|------------------------------|--------------------------------------------------|----------------------------------|
| External Input Connector     | Ingests information from external sources        | CVE, MISP, TheHive, MITRE        |
| Stream Connector             | Consumes platform data stream                    | History, Tanium                  |
| Internal Enrichment Connector| Enriches new OpenCTI entities from user requests | Observables enrichment           |
| Internal Import File Connector| Extracts information from uploaded reports      | PDFs, STIX2 Import               |
| Internal Export File Connector| Exports data into different file formats        | CSV, STIX2 export, PDF           |

Refer to the [OpenCTI connectors documentation](https://www.opencti.io/docs/connectors/) and [data model documentation](https://www.opencti.io/docs/data-model/) for more details on configuring connectors and the data schema.

*Source: OpenCTI Public Knowledge Base*