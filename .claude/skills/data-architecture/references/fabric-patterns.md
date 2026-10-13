# Microsoft Fabric Patterns

Practical guidance from James Serra's 2026 blog posts on Fabric. Fabric features change fast; verify the current state in Microsoft Learn before committing a design.

## Workspaces vs capacities

* A **workspace** is a boundary for content, ownership, security and deployment.
* A **capacity** is a pool of compute (Capacity Units) underneath one or more workspaces.
* The two do not need to map one to one. Many workspaces can share a capacity, and a few critical workspaces can sit on their own.

## When a capacity runs out: optimise, scale up, scale out, or isolate

First work out which problem you have: waste, growth or isolation.

| Problem | Response | Notes |
| --- | --- | --- |
| **Waste** | Optimise | Find expensive refreshes, queries, Spark jobs, over frequent pipelines, dev work on prod capacity. Usually the cheapest fix. |
| **Growth** | Scale up | Healthy workloads that collectively need more compute. Resizing is quick and reversible, so do not size for the worst five minutes of the year. Does not fix noisy neighbours. |
| **Different workload classes** | Scale out | Separate capacities by criticality and behaviour (production reporting, self service, dev and test, heavy engineering). Not one capacity per department by default. |
| **Risky or critical workloads** | Isolate | See patterns below. |

Isolation patterns:
* **Tryout capacity:** small SKU where new workloads prove their consumption before promotion to production.
* **Timeout capacity:** move a misbehaving workspace off shared capacity while its owner fixes it.
* **Rescue capacity:** a paused SKU you resume during an incident and reassign priority workspaces to. Not free: pausing reconciles smoothed usage and storage still bills.

Supporting practices:
* Understand bursting and smoothing. A short spike above 100 percent is normal; sustained accumulation leads to throttling.
* Install the Capacity Metrics app from day one and configure usage alerts before users complain.
* Use chargeback or showback for accountability.
* Guardrails such as surge protection, Spark autoscale billing and a dedicated Copilot capacity are useful, but each should answer a specific question about what you are protecting.

A typical starting topology: general purpose production capacity sized for normal demand, a separate self service capacity, a small dev, test and tryout capacity, and optionally a paused rescue capacity.

## Shortcuts vs mirroring vs pipelines

```
Source already Delta or Iceberg?
  Yes: shortcut for zero copy access
  No:  continue
Source is CSV, JSON, Parquet or Excel files?
  Only Spark or file access needed: Files shortcut
  Need tables: shortcut transformation or load to Delta
Source is unstructured .txt needing summarise, translate, PII redaction, entity extraction?
  Consider AI shortcut transformation (preview)
Source is a supported operational database?
  Ongoing replication: Mirroring ("mirror once, shortcut many")
  Controlled movement: Data Factory
Otherwise: Data Factory, notebook, dataflow or other ingestion
```

Shortcut rules:
* A shortcut under **Tables** should point to a valid Delta table (Parquet files plus a transaction log). A folder of Parquet is not a Delta table.
* A shortcut under **Files** exposes files only. It does not make them SQL queryable or Direct Lake ready.
* You do not need a shortcut just because data is in another workspace; Spark and three part T SQL names can often read directly. Use a shortcut when you want remote tables to appear through the consuming lakehouse's SQL endpoint and Direct Lake.
* Across tenants, use External Data Sharing rather than ad hoc access.
* Iceberg tables (for example Snowflake managed) can be read through shortcuts via metadata virtualisation.

Shortcut tradeoffs:
* Performance depends on source location, latency, file design and the reading engine. Cold reads from external clouds are slower.
* OneLake shortcut caching helps repeated reads for some external sources, fills file by file, and skips files over 1 GB.
* Source moves, deletes, permission changes and schema changes break consumers.
* You lose control of table maintenance on data you do not own.
* Copy when predictable performance, independent availability, residency or maintenance control matter more than zero copy.

## Shared data workspace pattern

* Put genuinely enterprise wide conformed data (calendar, organisation, geography, currency, reference data, master entities) in a shared workspace.
* Keep domain specific data with the owning domain.
* Consumer workspaces shortcut to both. An executive lakehouse can combine local tables with shortcuts to domain and shared data.
* Do not let the shared workspace become a dumping ground.

## Security

* OneLake security aims to define access once at the data layer instead of per engine. Prefer it over repeating permissions in every experience, and verify which engines honour it for your scenario.
* Row and column level security belongs as close to the data as possible.
* Never rely on agent or Copilot instructions for security.

## Power BI and semantic models

* One certified semantic model per business area, with described tables, columns and measures.
* Choose storage mode deliberately (Import, DirectQuery, Direct Lake) based on freshness, volume and concurrency.
* Push transformations upstream (lakehouse or warehouse) rather than into each model's Power Query when they are shared.
* Use Prep data for AI on the model for AI facing definitions (see `ai-ready-data.md`).
