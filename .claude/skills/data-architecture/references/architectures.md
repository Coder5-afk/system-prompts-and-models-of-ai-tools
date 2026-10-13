# Choosing a Data Architecture

Serra's book frames four main architectures. They are not mutually exclusive, and most real estates blend them. The point is to understand what each optimises for so the blend is deliberate.

## The four architectures

### Modern Data Warehouse (MDW)
* **Shape:** a data lake for landing and staging raw data, feeding a relational data warehouse that serves curated, modelled data.
* **Strengths:** mature, well understood, strong relational performance, familiar SQL skills, good security and governance on the warehouse side.
* **Weaknesses:** two storage technologies to manage, data copied between lake and warehouse, the warehouse can become a bottleneck for semi structured and data science workloads.
* **Fits when:** mostly structured data, strong SQL skills, reporting is the dominant use case, a central team owns the platform.

### Data Fabric
* **Shape:** an evolution of the MDW that adds technologies around it: metadata catalog, data virtualisation, real time ingestion, master data management, APIs, governance and access policies, so data can be found and used wherever it lives.
* **Strengths:** handles many source types and access patterns, reduces unnecessary copies through virtualisation, strong discovery and governance.
* **Weaknesses:** more moving parts, more skills required, higher cost and complexity. Virtualisation has performance and source dependency tradeoffs.
* **Fits when:** many heterogeneous sources, need for real time plus batch, strong governance and discovery requirements, a capable central team.

### Data Lakehouse
* **Shape:** a single lake storage layer using an open transactional table format (Delta, Iceberg, Hudi) that provides warehouse features (ACID, schema enforcement, time travel) directly on the lake. The relational warehouse becomes optional.
* **Strengths:** one copy of data, open formats, serves BI and data science from the same store, lower cost, fewer moving parts than MDW.
* **Weaknesses:** some relational features and concurrency behaviours can still lag a dedicated warehouse; security and governance must be designed carefully on files and tables; requires Spark or similar skills unless the platform hides it.
* **Fits when:** mixed structured and unstructured data, data science and BI on the same data, desire for open formats, cost sensitivity. This is the default starting point on most modern platforms (Fabric, Databricks).

### Data Mesh
* **Shape:** not a technology but a sociotechnical approach. Four principles: **domain ownership**, **data as a product**, **self serve data platform**, **federated computational governance**. Each domain builds and serves its own data products on a shared platform.
* **Strengths:** scales ownership with the organisation, removes the central team bottleneck, puts accountability with the people who understand the data.
* **Weaknesses:** demands domain level engineering skills, strong platform engineering, mature governance and a real cultural shift. Risk of duplication, inconsistent definitions and higher total cost. Serra is candid that many organisations are not ready, and that a mesh "in name only" adds cost without benefit.
* **Fits when:** large, genuinely decentralised organisation; domains have skilled teams and budget; central team is a proven bottleneck; executive sponsorship for the operating model change.

## Decision matrix

| Factor | MDW | Data Fabric | Lakehouse | Data Mesh |
| --- | --- | --- | --- | --- |
| Data mostly structured | Strong | Strong | Strong | Depends on domains |
| Lots of semi or unstructured data | Weak | Good | Strong | Depends on domains |
| Real time needs | Add on | Strong | Good | Depends on platform |
| Number of distinct sources | Low to medium | High | Medium to high | High |
| Central team capacity | Required | Required, larger | Required | Platform team only |
| Domain engineering skills | Not needed | Not needed | Not needed | Required in every domain |
| Organisational structure | Centralised | Centralised | Centralised or hybrid | Decentralised |
| Complexity and cost | Medium | High | Low to medium | High |
| Time to first value | Medium | Slow | Fast | Slow |

## Questions that usually settle it

1. **Is the organisation actually decentralised, with skilled domain teams?** If not, do not start with mesh. Start centralised and adopt mesh principles (product thinking, ownership, contracts) incrementally.
2. **Is a dedicated relational warehouse required** for concurrency, specific SQL features or skills? If not, lakehouse is usually simpler.
3. **How many sources must stay where they are** (residency, cost, ownership)? Many such sources push toward fabric style virtualisation.
4. **What is the dominant consumer?** Governed reporting favours a strong curated layer and semantic model regardless of pattern.

## Hybrid patterns worth naming

* **Lakehouse with domain oriented serving layer:** central platform and ingestion, but curated and serving layers organised by domain with named owners. A practical step toward mesh.
* **Federated lakehouse on a shared platform:** each entity or domain has its own workspaces and capacity, with shared conformed data (calendar, organisation, geography, reference data) published centrally and consumed by reference.
* **Hub and spoke:** central hub holds enterprise conformed data and master data; spokes hold domain specific data and consume the hub.

Always state which pattern you chose, what you gave up, and which conditions would make you revisit it.
