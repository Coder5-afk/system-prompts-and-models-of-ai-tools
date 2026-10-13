# Layers, Modelling and Data Movement

## Lake layers

Serra describes the lake as a series of layers, each with a clear purpose. Names vary (raw, conformed, cleansed, presentation; or bronze, silver, gold). Agree one convention per organisation and document it as a standard.

| Layer | Purpose | Typical rules |
| --- | --- | --- |
| **Raw / Bronze** | Land data exactly as received | Immutable, append only, partitioned by load date, original format retained, restricted access |
| **Conformed** | Convert to a common format (for example Delta) without changing content | Schema applied, types fixed, no business logic |
| **Cleansed / Silver** | Fix quality, deduplicate, standardise codes, apply master data | Business keys resolved, history tracked where needed, quality checks enforced |
| **Presentation / Gold** | Business ready, modelled for consumption | Star schemas, aggregates, data products, named owner, documented definitions |

Guidance:
* Do not skip the raw layer. It is the audit trail and the replay source.
* Keep business logic out of raw and conformed.
* Every gold asset has an owner, a definition and a refresh SLA.
* Sandboxes and exploration areas are separate from the governed layers.

## ETL vs ELT

* **ETL:** transform before loading. Suits constrained targets and strict curation.
* **ELT:** load raw first, transform inside the platform. Default on modern lake and lakehouse platforms because compute scales and raw data is retained for replay.
* **Reverse ETL:** push curated data back to operational systems. Treat it as a product with an owner and SLA.

## Modelling approaches

| Approach | Idea | Use when |
| --- | --- | --- |
| **Kimball (dimensional)** | Bottom up, business process star schemas, conformed dimensions shared across facts | Reporting and BI focus, fast delivery, semantic models and Power BI |
| **Inmon (normalised EDW)** | Top down, normalised enterprise warehouse feeding departmental marts | Strong central integration need, many consumers, stable enterprise model |
| **Data Vault** | Hubs (business keys), links (relationships), satellites (attributes with history) | Many changing sources, full auditability, parallel loading, regulatory history needs. Usually still needs a dimensional layer on top for consumption |
| **Common data model / ontology** | Shared business entity definitions across systems | Cross domain integration, AI grounding, interoperability across entities |

A common practical stack: Data Vault or normalised integration in silver, Kimball star schemas in gold, a semantic model or ontology on top.

## Conformed and master data

* Identify the entities that cross domains (customer, employee, organisation unit, location, product, calendar). These need a single authoritative definition and key.
* Decide the master data approach: registry, consolidation, coexistence or centralised.
* Publish conformed dimensions once and reference them. Do not let each domain build its own calendar or organisation hierarchy.

## Semantic layer

* The semantic layer (Power BI semantic model, Foundry ontology, dbt metrics, etc.) is where business meaning is fixed once: names, measures, relationships, hierarchies, synonyms.
* Prefer one certified semantic model per business area over many report specific models.
* Every measure has a written definition, owner and test.
* The semantic layer is also the main input for AI over data. A well described model is AI ready; a poorly described one is not, whatever instructions you add.

## Ingestion choices (general)

| Situation | Approach |
| --- | --- |
| Source already in an open table format | Reference in place (shortcut or virtualisation) |
| Operational database needing ongoing replication | Change data capture or mirroring |
| Files arriving in batches | Land in raw, convert to table format |
| Complex transformation, orchestration, custom schedules | Pipeline or orchestration tool |
| Events or telemetry | Streaming ingestion to a real time store, then to the lake |

## Anti patterns to flag

* Business logic in raw ingestion code
* Reports reading directly from raw or silver
* Several semantic models defining the same KPI differently
* No owner on a gold table
* Manual file drops with no lineage
* Copying data "just in case" with no consumer
