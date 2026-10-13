# Architecture Review Checklist

Rate each item: Met, Partial, Not met, N/A. For every Partial or Not met, record evidence, impact, severity (Critical, Major, Minor) and a fix.

## 1. Business alignment
- [ ] Linked to a named business outcome and capability
- [ ] Sponsor and product owner identified
- [ ] Success measures defined

## 2. Requirements
- [ ] Nonfunctional requirements are numeric (latency percentiles, volumes, RPO, RTO, concurrency)
- [ ] Latency need justified per use case
- [ ] Growth over three years considered

## 3. Architecture pattern
- [ ] Pattern named (MDW, fabric, lakehouse, mesh, hybrid) with rationale
- [ ] Alternatives and tradeoffs recorded as ADRs
- [ ] Complies with enterprise standards, or exceptions recorded with expiry

## 4. Data flow and storage
- [ ] System of record named for every core entity
- [ ] Derived stores are rebuildable from source
- [ ] No dual writes; CDC or outbox used where needed
- [ ] Layer conventions followed (raw, conformed, curated, presentation)
- [ ] No business logic in raw ingestion
- [ ] Partitioning and file sizing appropriate; hot keys considered
- [ ] Copy vs reference decisions justified

## 5. Processing
- [ ] Batch jobs idempotent and replayable
- [ ] Streaming handles late, duplicate and out of order events
- [ ] Orchestration, retries and alerting defined
- [ ] Schema evolution rules and data contracts in place

## 6. Modelling and semantics
- [ ] Conformed dimensions and master data reused, not rebuilt
- [ ] One certified semantic model or ontology per business area
- [ ] Every KPI has a written definition, owner and test
- [ ] AI readiness: descriptions, synonyms, grain, relationships documented

## 7. Security and privacy
- [ ] Classification of sensitive data done
- [ ] Access enforced at data layer (RBAC or ABAC, row and column level)
- [ ] No reliance on prompts or descriptions for security
- [ ] Data minimisation and retention defined
- [ ] Residency and sovereignty requirements met

## 8. Governance
- [ ] Assets registered in catalog with owners
- [ ] Lineage captured for critical pipelines
- [ ] Data quality checks on curated assets with visible results
- [ ] Access requests through recorded workflow

## 9. Operations and cost
- [ ] Monitoring, alerting and runbooks
- [ ] Failure scenarios tested (node, zone, region, bad deploy)
- [ ] Capacity or compute sizing justified; isolation of critical workloads
- [ ] Cost model and chargeback or showback

## 10. Delivery and evolvability
- [ ] Infrastructure as code and CI/CD for pipelines and models
- [ ] Dev, test and prod separation
- [ ] Documentation as code next to the system
- [ ] Clear owner for run and change after go live

## Summary

| Severity | Count | Top items |
| --- | --- | --- |
| Critical | | |
| Major | | |
| Minor | | |

Recommendation: Approve / Approve with conditions / Rework
