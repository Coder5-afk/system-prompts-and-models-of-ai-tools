# Palantir Foundry and Ontology Architecture

Sources:
* **Satoshi Yamauchi, *The Palantir Impact: Ontology Strategy Connecting Data and AI*** (open source, CC BY 4.0, github.com/Leading-AI-IO/palantir-ontology-strategy)
* **Kai, *Palantir Foundry by Use Cases: An Introduction to Contour, Reports, Fusion and Recipes*** (self published)
* **IoT and time series patterns:** drawn from general Foundry platform practice. The book *Reconnaissance and IoT Data Processing with Palantir Foundry* was requested as a source but could not be located; add its specifics here when available.

Summarised in our own words. Foundry features change quickly; check Palantir documentation before committing a design.

## 1. Ontology as an operational layer

The ontology strategy book argues that traditional lakes and warehouses tend to become places where data is only looked at. Foundry's ontology instead models the organisation as an operational digital twin, so data drives operations rather than ending in a dashboard.

**Nouns and verbs**

| Side | Elements | Captures |
| --- | --- | --- |
| **Nouns (semantics)** | Object types, properties, link types | The state of real world entities: employees, entities, cases, assets, sensors |
| **Verbs (kinetics)** | Actions, functions, writeback, dynamic security | What can be done: approve, assign, change status, raise an alert |

Design implication: model the decisions and actions users take, not just the tables they read. If a workflow has no action types, it is a reporting solution on Foundry, not an operational one.

**Governance of reality**
* Changing operations through the ontology needs software style controls: branching, review, versioning and audit.
* This is what lets AI agents propose changes quickly while humans approve them.
* Every action type should define who may run it, what it validates, what it writes, and what it logs.

## 2. Foundry layered architecture

| Layer | Foundry components | Equivalent concept |
| --- | --- | --- |
| Ingestion | Data Connection, agents, syncs, streaming sources | Raw / bronze |
| Transformation | Pipeline Builder, Code Repositories (Python, SQL, Java), transforms | Silver |
| Curated datasets | Clean, documented, owned datasets | Gold |
| Ontology | Object types, links, actions, functions | Semantic layer plus operational model |
| Applications | Workshop, Slate, Quiver, Contour, Fusion, Reports | Consumption |
| AI | AIP Logic, Agent Studio, AIP assist | AI over the ontology |
| Governance | Projects, markings, roles, lineage, Data Health | Security and governance |

Principles:
* Datasets are read only and versioned. Change data through transforms or synced sources, never by editing.
* Lineage is built in; keep it complete by avoiding manual uploads for production data.
* Back object types with curated datasets, not raw ones.
* Keep one object type per real world entity across use cases. Duplicate object types are the ontology equivalent of conflicting semantic models.

## 3. Self service analytics on Foundry (from *Foundry by Use Cases*)

The book focuses on helping business analysts move from document centric spreadsheet work to data centric work.

| Tool | Use for |
| --- | --- |
| **Contour** | Point and click analysis on datasets: filter, join, aggregate, pivot, chart, save results as new datasets |
| **Reports** | Shareable, auto refreshing reports built from analyses |
| **Fusion** | Spreadsheet interface whose cells can write back to datasets; useful for planning data and manual inputs with governance |
| **Recipes / Pipeline Builder** | Repeatable, low code transformations |
| **Scheduling** | Rebuilds triggered by time or by upstream dataset updates |
| **Historisation** | Snapshotting data over time so trends and point in time views are possible |
| **Slate / Workshop** | Custom applications when analysis needs interaction |

Practices:
* Separate **readers** (consume predefined reports) from **editors** (build analyses). The degree of self service depends on how many editors you enable and train.
* Teach problem solving patterns (how to get from question to dataset to analysis), not just tool clicks.
* Replace manual spreadsheet handoffs with Fusion writeback into governed datasets so lineage is preserved.
* Schedule on upstream updates rather than fixed times where possible, so reports refresh when data actually changes.
* Plan historisation explicitly. Foundry datasets show current state unless you design snapshots or incremental history.

## 4. IoT, sensor and time series patterns

General Foundry practice for high volume telemetry, sensor and observation data:

* **Ingest** streams through Foundry streaming sources or connectors from Kafka, MQTT gateways or event hubs. Land raw events unchanged with event time and ingest time.
* **Process** with streaming transforms for near real time needs and incremental batch transforms for heavier processing. Handle late, duplicate and out of order events explicitly (see `data-systems-foundations.md`).
* **Model** sensors and assets as object types; store readings as **time series** linked to those objects rather than as millions of objects.
* **Geospatial:** keep coordinates and geometry as typed properties so map applications and geospatial functions work.
* **Alerting:** define thresholds or models that create alert objects and trigger actions, so detection leads straight to an operational workflow.
* **Retention and downsampling:** keep raw at full resolution for a defined window, then aggregate. Document the policy.
* **Edge and disconnected:** for constrained or air gapped environments, plan local processing and later sync, and decide what is authoritative when connectivity returns.

## 5. Ontology design checklist

1. Start from the operational decisions and workflows, then derive object and action types.
2. Name object types in business language; describe every property.
3. Use stable primary keys from master data; never from display names.
4. Model relationships as link types with clear cardinality.
5. Define actions with validation rules, permissions, side effects and audit.
6. Apply markings and row level controls at the object and property level, not in applications.
7. Keep one shared core ontology (people, organisations, locations, assets) owned centrally; let domains extend it.
8. Version and review ontology changes like code.
9. Give AI agents access through the ontology and actions with human approval on writes.
10. Measure adoption by operational use (actions taken), not just objects created.

## 6. Foundry in a mixed estate

* **Foundry vs lakehouse:** a lakehouse (Fabric, Databricks) is strong for broad analytics and BI at low cost per GB; Foundry is strong where you need an operational ontology, governed writeback and decision workflows. Many enterprises run both.
* **Integration:** use open formats and virtual tables where supported to avoid copying, and decide a single system of record per entity.
* **Interoperability:** publish conformed reference data once and share it to both platforms; avoid each platform maintaining its own organisation hierarchy or employee master.
* **Lock in:** the ontology encodes a lot of business logic. Document it outside the platform as well (glossary, decision records) so it is portable.

## Deeper Foundry guidance

For official Palantir guidance on ontology design, pipelines, security and AIP, see `foundry-ontology-design.md`, `foundry-pipelines-security.md` and `foundry-aip.md`.
