# Data Engineering Lifecycle

Distilled from the concepts in **Joe Reis and Matt Housley, *Fundamentals of Data Engineering* (O'Reilly)**. Summarised in our own words. Use it to check that a design covers the full lifecycle, not just the storage and transformation parts that usually get the attention. It maps directly onto Foundry (Data Connection, transforms, ontology, applications), Fabric and Databricks.

## The lifecycle

| Stage | Key questions |
| --- | --- |
| **Generation** | What are the source systems? Who owns them? How is data produced, at what rate, with what schema and quality? How will we hear about source changes? |
| **Storage** | Which storage systems fit the access pattern (OLTP, object storage, warehouse, lakehouse, streaming log)? Hot vs cold data? Retention? |
| **Ingestion** | Batch or streaming? Push or pull? Full or incremental (CDC)? Idempotent? What happens when the source schema changes? |
| **Transformation** | Where does business logic live? Which modelling approach? How are transformations tested and versioned? |
| **Serving** | Analytics, ML, operational apps, reverse ETL, APIs, AI agents. Who consumes, and with what SLA? |

Storage runs underneath every stage; it is not a single step.

## The undercurrents

Six concerns that cut across every stage. A design that ignores any of them is incomplete.

* **Security:** least privilege, encryption, identity, audit. Security is a people and process problem as much as a technical one.
* **Data management:** governance, discoverability, lineage, quality, master data, modelling, privacy, retention, ethics.
* **DataOps:** automation (CI/CD for pipelines and models), observability and monitoring, incident response. Apply DevOps and lean thinking to data.
* **Data architecture:** the design that supports the organisation's evolving needs (principles below).
* **Orchestration:** scheduling and dependency management across jobs, with retries, backfills and alerting.
* **Software engineering:** version control, testing, code review, infrastructure as code, open source awareness.

## Principles of good data architecture

1. **Choose common components wisely.** Shared components (object storage, catalog, orchestration, identity) should be flexible and broadly useful.
2. **Plan for failure.** Know availability, reliability, RTO and RPO. Assume things break.
3. **Architect for scalability.** Scale up and, as important, scale down to save cost.
4. **Architecture is leadership.** Architects mentor and guide; they do not just draw.
5. **Always be architecting.** Architecture evolves with the business; there is no final state.
6. **Build loosely coupled systems.** Teams and components can change independently through clear interfaces.
7. **Make reversible decisions.** Prefer "two way doors" so you can change course cheaply. Reserve heavy process for irreversible ones.
8. **Prioritise security.** Zero trust, shared responsibility with cloud providers, every engineer is a security engineer.
9. **Embrace FinOps.** Cost is an architectural property. Monitor spend, make it visible, and design to it.

## Technology choice

* **Team size and capability first.** Small teams should favour managed and simpler tools.
* **Speed to market.** Value delivered early beats a perfect platform late.
* **Interoperability.** Open formats and standard interfaces reduce lock in.
* **Cost optimisation and business value.** Consider total cost of ownership and opportunity cost, not licence price alone.
* **Immutable vs transitory technologies.** Bet on durable foundations (SQL, object storage, open table formats, networking basics); treat fast moving tools as replaceable.
* **Build vs buy.** Build only where it creates competitive advantage; buy or adopt for commodity capabilities.
* **Monolith vs modular.** Modular systems are more flexible but harder to integrate; be deliberate.
* **Serverless vs servers.** Choose based on workload shape and cost at scale.

## Type A and Type B data engineering

* **Type A (abstraction):** use managed services and off the shelf tools to keep architecture simple.
* **Type B (build):** build custom tools and systems where data is a core competitive asset.
Most enterprises, including most public sector organisations, should lean Type A.

## Lifecycle review questions

* Is there a contract or at least a communication channel with each source system owner?
* Is every ingestion idempotent and incremental where possible?
* Are transformations in version control with tests and CI/CD?
* Are orchestration, retries, backfills and alerting defined?
* Is every serving endpoint owned with an SLA?
* Is cost visible per workload and per team?
* Which decisions are reversible, and are the irreversible ones getting the right level of scrutiny?
