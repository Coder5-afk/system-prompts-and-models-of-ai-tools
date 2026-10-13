# Data Systems Foundations

Distilled from the concepts in **Martin Kleppmann and Chris Riccomini, *Designing Data-Intensive Applications*, 2nd edition (O'Reilly, 2026)**. This is the engineering layer underneath every architecture pattern: how data is stored, moved, replicated, partitioned and kept correct. Use it to judge whether a design will actually work under load, failure and change, not just whether it looks right on a diagram.

The book's central idea: every data system choice is a tradeoff between reliability, scalability, maintainability, consistency and efficiency. There are no free properties. Name the property you are buying and the one you are paying with.

## 1. Define the nonfunctional requirements first

Before choosing a store or engine, write down measurable targets:

* **Reliability:** what failures must the system tolerate (disk, node, zone, region, bad deploy, human error)? What is the recovery point and recovery time objective?
* **Scalability:** describe load with concrete parameters (requests per second, read to write ratio, rows per day, concurrent users, fan out). Describe growth.
* **Performance:** use percentiles, not averages. State p50, p95, p99 targets. Tail latency matters most when one user request fans out to many backends.
* **Maintainability:** operability (easy to run), simplicity (easy to understand), evolvability (easy to change).
* **Cost and efficiency:** compute, storage, egress, licences, and people.

A design without numeric targets cannot be reviewed.

## 2. Operational vs analytical systems

* **OLTP (operational):** many small reads and writes by key, latest state, low latency, serves applications.
* **OLAP (analytical):** large scans and aggregations over history, serves analysts, BI and ML.
* Keep them separate. Feed analytics from operational systems through change data capture, events or batch extracts, not by querying production databases directly.
* **Systems of record vs derived data:** be explicit which store is the source of truth for each entity. Everything else (warehouse, cache, search index, vector index, feature store) is derived and must be rebuildable from the source.

## 3. Data models

| Model | Strengths | Watch for |
| --- | --- | --- |
| Relational | Joins, constraints, mature tooling, many to many relationships | Object relational mismatch, schema rigidity if poorly managed |
| Document | Locality for tree shaped data, flexible schema | Weak joins, duplication, schema on read still means a schema exists |
| Graph | Highly connected data, traversals, ontologies | Fewer engines, harder to scale writes |
| Event log | Full history, replay, audit, decoupling | Requires derived views for querying |
| Dataframe / columnar | Analytics, ML | Not for transactional updates |

Schema on read is still a schema. It is just enforced by every reader instead of once by the writer.

## 4. Storage engines

* **Log structured (LSM trees):** fast writes, compaction in the background. Common in NoSQL and lake table formats.
* **B trees:** fast reads, in place updates. Common in relational databases.
* **Column oriented storage:** compresses well and scans only needed columns. Basis of warehouses, Parquet, Delta, Iceberg and VertiPaq.
* Practical consequences for lake tables: small files hurt, compaction and optimisation matter, partitioning must match query filters, and sort order or clustering affects scan cost.

## 5. Encoding and schema evolution

* Data outlives code. Old and new versions of applications will read and write the same data at the same time.
* Require **backward compatibility** (new code reads old data) and **forward compatibility** (old code reads new data).
* Use formats with explicit schemas and evolution rules (Avro, Protobuf, Parquet, Delta, Iceberg). Avoid ad hoc JSON for contracts between teams.
* Add fields as optional with defaults. Never reuse a field identifier. Never change a field's meaning silently.
* Treat schemas between producers and consumers as **data contracts** with versioning and an owner.

## 6. Replication

| Approach | Use | Risk |
| --- | --- | --- |
| Single leader | Most databases; simple consistency | Leader failover, replication lag |
| Multi leader | Multi region writes, offline clients | Write conflicts need resolution |
| Leaderless (quorum) | High availability writes | Tuning quorums, stale reads, conflict handling |

Replication lag creates anomalies users notice: not reading their own writes, seeing data go backwards, causally related events out of order. Decide which guarantees each use case needs (read your writes, monotonic reads, consistent prefix).

## 7. Partitioning (sharding)

* Partition by key range (good for range scans, risk of hot spots) or by hash (even spread, lose range scans).
* Watch for **skew and hot keys**. One very active key can saturate a partition regardless of total capacity.
* Secondary indexes on partitioned data are either local (cheap writes, scatter gather reads) or global (fast reads, more complex writes).
* Rebalancing must be planned. Moving data is expensive and risky.

## 8. Transactions and isolation

* Know which isolation level the store actually provides. Names are inconsistent across vendors.
* Common anomalies: dirty reads, lost updates, write skew, phantoms.
* **Read committed** and **snapshot isolation** prevent some anomalies but not write skew. **Serializable** prevents all but costs throughput.
* Lake table formats provide ACID per table, not across tables. Multi table consistency needs design (for example a single commit marker, or idempotent replays).

## 9. Distributed systems realities

* Networks drop, delay and duplicate messages. Timeouts cannot distinguish a slow node from a dead one.
* Clocks drift. Do not rely on wall clock time to order events across machines.
* Processes pause (garbage collection, VM migration). A node may think it still holds a lock it has lost; use fencing tokens.
* Design for partial failure as the normal case.

## 10. Consistency and consensus

* **Linearizability** makes a distributed system behave as if there were one copy. It is expensive and reduces availability during network partitions.
* **Causal consistency** is often sufficient and cheaper.
* **Consensus** (leader election, atomic commit, unique constraints across nodes) is solved by tools such as ZooKeeper, etcd and Raft based systems. Use them; do not reinvent them.
* Be precise about guarantees. "Eventually consistent" says nothing about how long.

## 11. Batch processing

* Treat inputs as immutable and outputs as fully derived. Rerunning a job must produce the same result.
* Prefer idempotent, deterministic jobs that overwrite a partition rather than appending blindly.
* Separate the logic (what to compute) from the scheduling (when to compute it).
* Keep raw inputs so any derived dataset can be rebuilt after a bug fix.

## 12. Stream processing

* An event log (Kafka, Event Hubs, Eventstream) is the backbone that lets many consumers derive their own views independently.
* **Change data capture** turns a database into a stream, keeping derived systems in sync without dual writes.
* **Event sourcing** stores every change as an immutable event and derives current state from them.
* Handle event time vs processing time, late arriving data, and windowing explicitly.
* Delivery semantics: at most once, at least once, effectively once. Effectively once needs idempotent consumers or transactional sinks.
* **Avoid dual writes** (writing to a database and a queue in application code). Use CDC or an outbox pattern instead.

## 13. Unbundling the database

The book's synthesis: a modern data platform is a set of specialised stores (OLTP database, log, lake, warehouse, cache, search, vector index) connected by derivation. Design it like that:

* One system of record per entity
* Every other store is a derived view fed by logs or CDC
* Derivation is deterministic and replayable
* Correctness is checked end to end, not assumed from each component

## 14. Doing the right thing

The book closes on ethics and responsibility: privacy, consent, data minimisation, the risk of automated decisions and surveillance, and the duty to consider who is affected by the data. For public sector systems this maps directly to purpose limitation, retention rules and explainability of eligibility or scoring decisions.

## Review checklist from this lens

* Are nonfunctional requirements numeric, with percentile latency targets?
* Is each entity's system of record named, and is everything else rebuildable?
* Are schemas versioned with compatibility rules and owners?
* Does the design rely on a consistency or isolation guarantee the store does not provide?
* Are there dual writes anywhere?
* Are batch jobs idempotent and replayable?
* Do streams handle late data, duplicates and reordering?
* Are hot keys and skew considered in partitioning?
* What happens on node, zone and region failure, and has it been tested?
* Is personal data minimised, retained only as long as needed, and traceable?
