# Data Management Body of Knowledge

Distilled from the concepts in **DAMA International, *DAMA-DMBOK: Data Management Body of Knowledge*, 2nd edition**. Summarised in our own words. Use it as the checklist of data management functions an enterprise must cover, and to frame maturity assessments and operating models.

## The DAMA wheel: eleven knowledge areas

Data governance sits at the centre and guides the other ten.

| Knowledge area | What it covers | Typical deliverables |
| --- | --- | --- |
| **Data governance** | Authority, decision rights, policies, stewardship, accountability | Governance charter, council, policies, stewardship model |
| **Data architecture** | Enterprise data models, data flows, alignment with business strategy | Enterprise data model, target architecture, data flow maps |
| **Data modelling and design** | Conceptual, logical and physical models | Models, naming standards, model review process |
| **Data storage and operations** | Database design, operation, backup, recovery, performance | Runbooks, SLAs, backup and DR plans |
| **Data security** | Confidentiality, access control, privacy, classification | Classification scheme, access model, privacy controls |
| **Data integration and interoperability** | Movement and consolidation between systems | Integration patterns, interface standards, ETL and APIs |
| **Document and content management** | Unstructured content lifecycle, records management | Retention schedules, records policy, content repository |
| **Reference and master data** | Shared code sets and core entities | MDM hub or approach, reference data service, golden records |
| **Data warehousing and BI** | Analytical environments and reporting | Warehouse or lakehouse, semantic models, reports |
| **Metadata management** | Business, technical and operational metadata | Catalog, glossary, lineage |
| **Data quality** | Dimensions, measurement, improvement | Quality rules, scorecards, issue management |

## Environmental factors

For each knowledge area DMBOK expects you to define: goals and principles, activities, inputs, deliverables, roles and responsibilities, tools, techniques, and metrics. Use this as a template when defining any data management function.

## Governance operating models

* **Centralised:** one governance organisation for the enterprise.
* **Replicated:** each unit runs the same model independently.
* **Federated:** central body sets policy; units execute with local stewards.
Most large organisations, and nearly all multi entity governments, end up federated.

## Roles

| Role | Responsibility |
| --- | --- |
| Chief Data Officer | Strategy, accountability, sponsorship |
| Data governance council | Policy approval, issue escalation, priorities |
| Data owner | Business accountability for a data domain |
| Business data steward | Definitions, quality rules, glossary for a domain |
| Technical data steward | Implementation of standards in systems |
| Data custodian | Operates the systems that hold the data |

## Data quality dimensions

Completeness, uniqueness, validity, accuracy, consistency, timeliness, integrity. Define which dimensions matter for each critical data element and measure them.

## Master and reference data approaches

| Style | How it works | Fits when |
| --- | --- | --- |
| Registry | Index pointing to records in source systems | Low disruption, read only consolidation |
| Consolidation | Golden record built in a hub for analytics | Analytics need one view; sources unchanged |
| Coexistence | Hub and sources synchronise both ways | Gradual move to managed master data |
| Centralised (transactional) | Hub is the system of record | Strong control needed, sources can change |

## Maturity

Assess each knowledge area on a five level scale (initial, repeatable, defined, managed, optimised). Prioritise improvements by business impact, not by trying to raise everything at once.

## Using it in a review

For any platform or programme, check that each knowledge area has an owner, a policy and a deliverable. Missing areas are usually document management, reference data and metadata.
