# Databricks and Unity Catalog Patterns

Drawn from general Databricks platform practice. Databricks releases features quickly; verify against current Databricks documentation before committing a design.

## Core building blocks

| Component | Role |
| --- | --- |
| Delta Lake | Open ACID table format on object storage |
| Unity Catalog | Central governance: catalog, permissions, lineage, auditing, data sharing |
| Workspaces | Collaboration and compute environments attached to a metastore |
| Compute | Clusters, SQL warehouses (serverless or classic), job compute |
| Lakeflow / Delta Live Tables | Declarative pipelines with built in expectations |
| Workflows / Jobs | Orchestration |
| Databricks SQL | BI and SQL analytics |
| Delta Sharing | Open protocol for sharing data across organisations and platforms |
| Mosaic AI | Model serving, vector search, agents, evaluation |
| Genie | Natural language over curated data |

## Unity Catalog hierarchy

`metastore > catalog > schema > table / view / volume / function / model`

Common catalog layouts:
* **By environment:** `dev`, `test`, `prod` catalogs with identical schemas. Good isolation, simple promotion.
* **By domain or entity:** `hr`, `finance`, `procurement` catalogs. Good ownership alignment.
* **By layer:** `bronze`, `silver`, `gold` catalogs. Simple, but weaker ownership.
* Most enterprises combine: environment by workspace binding, domain by catalog, layer by schema.

## Security model

* Grant to **groups**, never to individuals. Sync groups from the identity provider.
* Use catalog and schema level grants for broad access; table grants for exceptions.
* **Row filters and column masks** for fine grained control.
* **Attribute based access control (ABAC)** with governed tags lets policies follow classification tags across tables. Prefer this at scale over per table rules.
* Bind catalogs to specific workspaces to keep prod data out of dev.
* Use managed tables by default; external locations only where data must stay in a specific storage account.

## Lineage and audit

* Unity Catalog captures column level lineage for workloads that run through it.
* System tables provide audit, billing, lineage and query history for observability and FinOps.

## Pipelines

* Use Lakeflow or DLT for declarative medallion pipelines with expectations (warn, drop, fail).
* Use Auto Loader for incremental file ingestion and Lakeflow Connect or CDC for databases.
* Keep business logic in version controlled code (Databricks Asset Bundles for CI/CD).

## Interoperability

* **Delta Sharing** to share with other organisations or platforms without copies.
* **Lakehouse Federation** to query external databases in place.
* Fabric can read Databricks Delta tables through OneLake shortcuts; Unity Catalog can be scanned by Purview. Decide which catalog is authoritative for which assets (catalog of catalogs).
* Foundry can connect to Databricks as a source; avoid maintaining the same curated tables on both.

## Cost

* Prefer serverless SQL warehouses and job compute with auto termination.
* Use cluster policies to cap sizes.
* Monitor spend by tag through system billing tables.

## Fabric vs Databricks vs Foundry (positioning)

| Need | Typically strongest |
| --- | --- |
| Microsoft 365 and Power BI centred BI with low ops | Fabric |
| Heavy engineering, Spark, ML and open governance | Databricks |
| Operational ontology, governed writeback, decision workflows | Foundry |

Many estates run two or three. The architecture question is which is the system of record for each curated dataset and how they share without duplication.
