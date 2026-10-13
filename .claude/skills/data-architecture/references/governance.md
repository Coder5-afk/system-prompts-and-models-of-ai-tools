# Data Governance

Based on James Serra's Purview best practices and his book's treatment of people and process. The tool examples use Microsoft Purview; the practices apply to Unity Catalog, Foundry or any catalog.

## Core belief

A governance tool does not deliver governance. The operating model around it does. A catalog nobody trusts, with no owners and stale metadata, will be abandoned.

## Practices

**Make registration part of creation**
* Every new source, data product or important dataset includes a "registered in the catalog?" step in its delivery checklist.
* Notify the catalog administrators when new sources appear so they are scanned. An incomplete catalog loses trust fast.

**Search before you build**
* Train report developers, engineers and analysts to search the catalog before building new reports, pipelines or datasets.
* Use role specific training with real examples, not a single demo.

**Route access through the catalog**
* Use the catalog's access request workflow instead of email and ad hoc tickets, so requests, approvals and reasons are recorded.

**Curate scanned metadata**
* Scans miss and misclassify things. Review results, add descriptions, friendly names, glossary terms and attributes.
* This manual curation is expected, not a failure of the tool.

**Glossary, contacts, domains, data products**
* Glossary terms link inconsistent technical names (customer, client, person, account holder) to one business meaning.
* Every asset has named owners and experts.
* Organise assets into governance domains and data products that match how the business thinks, not how servers are laid out.

**Manage lineage deliberately**
* Automatic lineage covers supported tools only. Manual movement, Excel exports and custom scripts are invisible unless pushed through APIs.
* Capture runtime lineage from what jobs actually did, especially for parameterised pipelines, dynamic SQL and notebooks.

**Act on findings**
* When classification finds sensitive data in an unexpected place, notify the owner and security, and confirm protection.
* Data quality scoring exposes problems; fixes happen at the source, pipeline or business process.

**Catalog of catalogs**
* Most enterprises run several catalogs (Purview, Unity Catalog, Foundry). Decide which owns what, how metadata flows between them, and where users go for enterprise wide discovery.

## Operating model

| Role | Accountability |
| --- | --- |
| Data owner | Business accountability for a domain or product; approves access; signs off definitions |
| Data steward | Day to day metadata, glossary, quality rules |
| Data custodian / engineer | Technical implementation, pipelines, security controls |
| Governance council | Cross domain standards, conflict resolution, priorities |
| Platform team | Catalog tooling, automation, self serve capabilities |

Governance models mirror architecture models: centralised, federated or hybrid. Data mesh requires federated computational governance, meaning standards are set centrally but enforced automatically by the platform.

## Minimum viable governance for a new platform

1. Named owner for every gold asset
2. Glossary for the top 20 to 50 business terms and KPIs
3. Classification of sensitive data with enforced access control
4. Catalog registration in the delivery checklist
5. Access requests through a recorded workflow
6. Data quality checks on gold assets with visible results
7. Lineage for critical pipelines
