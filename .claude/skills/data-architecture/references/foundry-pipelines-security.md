# Foundry Pipelines, Releases and Security

Based on Palantir's official Foundry documentation (palantir.com/docs/foundry): building pipelines, Data Connection, Global Branching, security overview, and securing a data foundation. Summarised in our own words as of October 2026.

## Pipeline stages and project structure

Split a pipeline into separate **projects by stage**, each with its own repository:

| Project | Contents | Typical access |
| --- | --- | --- |
| **Datasource** | Source connections, raw syncs, cleaning | Data engineers; others Discoverer |
| **Transform** | Reusable, integrated datasets | Data engineers, analysts as Viewer |
| **Ontology** | Datasets backing object types, the ontology definitions | Builders; operational users Viewer |
| **Use case / application** | Workshop apps, analyses, AIP workflows | Product team; end users through app |

Practices:
* Create groups per project mapped to roles (Owner, Editor, Viewer). Grant through groups, never to individuals.
* Set the project default role to **Discoverer** so people can find assets without reading them.
* Use **project references** to consume upstream datasets downstream, so users of the downstream project do not need access to the source project.
* Share by adding people to the right project group, not by sharing individual resources. It is uniform and auditable.
* For a multi entity platform, repeat this structure per entity or domain, with a shared set of conformed projects for reference data.

## Ingestion with Data Connection

* **Sources** define connections; **agents** run inside private networks to reach on premises systems.
* **Syncs** bring data in: batch, file based, media sets and streaming; JDBC syncs can be optimised for incremental loads.
* **Virtual tables** query external systems (for example Databricks, Snowflake, BigQuery) without copying, where supported.
* **Exports** and **webhooks** send data and events out to external systems.
* **Listeners** (HTTPS, WebSocket, email and SaaS integrations) receive inbound events.
* Land source data unchanged in the Datasource project; keep business logic downstream.

## Building transforms

| Tool | Best for |
| --- | --- |
| **Pipeline Builder** | Visual, low code pipelines with built in branching, schedules, health checks and unit tests; fastest for most integration work |
| **Code Repositories** | Python, Java or SQL transforms with full software engineering (tests, libraries, code review); complex logic, reuse, large teams |

Modes:
* **Batch:** recompute outputs from inputs.
* **Incremental:** process only new or changed data; essential as volumes grow. Design so a full rebuild still produces the same result.
* **Streaming:** low latency with keys and stateful transforms; use only where the latency is actually needed.

## Scheduling

* Prefer triggers on upstream dataset updates over fixed times where possible.
* Schedule at the level of a logical pipeline, not every dataset separately.
* Avoid overlapping schedules that rebuild the same datasets.
* Parameterise schedules for environments.

## Quality in production

* Define **data expectations** (schema, uniqueness, nulls, value ranges, row counts) on key outputs, and fail builds on critical violations.
* Configure **health checks** for freshness, build success and schema changes, with owners notified.
* Add unit tests to transforms.
* See `data-quality-observability.md` for the general approach.

## Branching and release

* **Global Branching** lets you change pipelines, ontology definitions, applications and automations together on one branch, test end to end, run actions without affecting main, and merge in one step after required reviews.
* Approval policies per resource decide who must review.
* **Release management** promotes versions across separate environments (development, test, production). Use branching for feature development and release management for promotion.
* Use **Marketplace** products to package and deploy a set of resources (pipelines, ontology, apps) repeatably to other spaces or environments, which suits rolling the same solution out to many entities.

## Security model

**Mandatory controls** (enforced by the system, propagate through lineage to derived data):
* **Organisations:** hard silos between groups of users and projects unless sharing is configured.
* **Markings:** for sensitive data (PII, financial, health). Users need the marking in addition to project access. Markings travel with derived data.
* **Classification based access controls:** for classified environments.

**Discretionary controls** (granted per resource, do not propagate):
* **Project roles:** Owner, Editor, Viewer, Discoverer.
* **Granular policies and restricted views:** row and column filtering for reads.
* **Object and property security policies:** row and column style rules on ontology objects and properties.

Key rule from the docs: discretionary row and column filters control what a user can **read**, but do not follow data into downstream outputs or exports. For sensitive data, always pair them with **markings**, which do propagate.

**Practices**
* Map identity provider groups to Foundry groups.
* Separate maintainers (Owner, Editor) from consumers (Viewer).
* Least privilege on Owner and Editor.
* Apply markings at ingestion for sensitive sources so every derivative inherits them.
* Use restricted views to give operational users entity filtered access.
* Review permissions with the platform's permission checking tools before go live.

## Pipeline review checklist (Foundry)

- [ ] Projects split by stage with group based roles
- [ ] Default role Discoverer; project references used downstream
- [ ] Raw sources landed unchanged; logic downstream
- [ ] Incremental where volume requires; full rebuild gives same result
- [ ] Expectations and health checks on key outputs with owners
- [ ] Schedules triggered by upstream updates, no overlaps
- [ ] Changes developed on branches with required reviewers
- [ ] Markings applied to sensitive data at ingestion
- [ ] Row and column policies paired with markings for downstream protection
- [ ] Marketplace packaging for repeatable multi entity rollout
