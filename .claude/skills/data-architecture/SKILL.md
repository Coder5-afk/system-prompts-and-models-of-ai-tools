---
name: data-architecture
description: Expert data and enterprise architecture. Covers choosing between modern data warehouse, data fabric, lakehouse and data mesh; distributed data systems fundamentals; the data engineering lifecycle; enterprise integration and Data Vault; Microsoft Fabric, OneLake and Palantir Foundry ontology design; AI ready data; governance; and running an enterprise architecture practice. Use for target architectures, ADRs, design and code reviews, platform choices, ontology design, standards or EA strategy.
---

# Data and Enterprise Architecture

A working method for data and enterprise architecture, built on these sources:

| Source | Contributes |
| --- | --- |
| James Serra, *Deciphering Data Architectures* (O'Reilly) | Choosing between MDW, data fabric, lakehouse, data mesh; design sessions |
| James Serra's blog, jamesserra.com | Current Fabric, OneLake, Purview and AI ready data practice |
| Martin Kleppmann and Chris Riccomini, *Designing Data-Intensive Applications*, 2nd ed. (O'Reilly, 2026) | Storage, replication, partitioning, transactions, consistency, batch and stream processing |
| Joe Reis and Matt Housley, *Fundamentals of Data Engineering* (O'Reilly) | Data engineering lifecycle, undercurrents, architecture principles, technology choice |
| W.H. Inmon, D. Linstedt, M. Levins, *Data Architecture: A Primer for the Data Scientist* | Enterprise integration, system of record, Data Vault, unstructured text |
| Tanu McCabe, *Fundamentals of Enterprise Architecture* (O'Reilly, 2024) | Running the architecture practice: alignment, decisions, standards, assessment, strategy |
| Satoshi Yamauchi, *The Palantir Impact: Ontology Strategy* (open source, CC BY 4.0) | Ontology as operational layer, nouns and verbs, governance of change |
| Kai, *Palantir Foundry by Use Cases* | Foundry self service: Contour, Reports, Fusion, recipes, scheduling, historisation |
| Palantir official documentation, palantir.com/docs/foundry | Ontology design principles and anti patterns, actions, Automate, OSDK, pipelines, branching, security model, AIP |
| Zhamak Dehghani, *Data Mesh* (O'Reilly) | Domain ownership, data products, self serve platform, federated computational governance |
| DAMA International, *DAMA-DMBOK*, 2nd ed. | Data management knowledge areas, governance roles, master data styles, maturity |
| Ralph Kimball and Margy Ross, *The Data Warehouse Toolkit*, 3rd ed. | Grain, facts, conformed dimensions, slowly changing dimensions, bus matrix |
| General practice: data quality and observability, Databricks and Unity Catalog, security and sovereignty, MLOps and LLM platforms (incl. Chip Huyen), Oracle Fusion HCM extraction | Platform and domain specifics; verify against current vendor documentation |

The guidance in the reference files is summarised and paraphrased, not quoted. Point readers to the books for depth.

**Philosophy:** there is no single right architecture. The right answer depends on business outcomes, people, process and technology. Every choice is a tradeoff, and every significant tradeoff is written down as a decision.

## When to use this skill

* Choosing between a modern data warehouse, data fabric, lakehouse or data mesh
* Producing a target architecture, architecture decision record (ADR) or design review
* Reviewing a repo, pipeline, dbt project, notebook set or IaC for architectural soundness
* Designing Fabric workspaces, capacities, OneLake shortcuts, mirroring or security
* Making data usable by Copilot, Fabric Data Agents, Foundry AIP or any LLM
* Setting up governance: catalog, glossary, lineage, ownership, access requests
* Setting up or improving an architecture practice: standards, decision process, review boards, EA strategy, capability assessments

## Two levels of work

| Level | Question it answers | Primary references |
| --- | --- | --- |
| **Systems foundations** | Will it actually work under load, failure and change? | `data-systems-foundations.md`, `data-engineering-lifecycle.md` |
| **Solution and data architecture** | What should we build, and how? | `design-session.md`, `architectures.md`, `data-mesh.md`, `modeling-and-layers.md`, `dimensional-modeling.md`, `inmon-enterprise-data.md` |
| **Platforms and sources** | How does it map to the technology we have? | `fabric-patterns.md`, `databricks-unity-catalog.md`, `palantir-foundry.md`, `foundry-ontology-design.md`, `foundry-pipelines-security.md`, `foundry-aip.md`, `oracle-fusion-hcm.md` |
| **Trust, security and AI** | Can people trust it, is it safe, and can AI use it? | `governance.md`, `dmbok-data-management.md`, `data-quality-observability.md`, `security-sovereignty.md`, `ai-ready-data.md`, `mlops-llm-platforms.md` |
| **Enterprise architecture practice** | How do we make good architecture decisions consistently across the organisation? | `enterprise-architecture.md`, `decision-framework.md`, `templates/ea-assessment.md` |

Most real requests touch both. A target data architecture is a solution deliverable; getting it adopted across 50 entities is an enterprise architecture problem.

## Core workflow for a design

Follow these steps in order. Do not jump to a technology before step 3.

1. **Anchor on business outcomes.** What outcome does this serve, and which capabilities does it need? Use the outcome, capability, gap chain in `references/enterprise-architecture.md`.
2. **Discover requirements.** Run the architecture design session in `references/design-session.md`. If the user is unavailable, list assumptions explicitly at the top of the output.
3. **Set numeric nonfunctional requirements and classify the problem.** Load, percentile latency, RPO and RTO, volume, velocity, variety, sources, consumers, organisation model, skills, regulatory and sovereignty constraints. See `references/data-systems-foundations.md`.
4. **Pick the architecture pattern.** Use the decision matrix in `references/architectures.md`. Name the pattern, why it fits, and what you are giving up.
5. **Walk the lifecycle.** Generation, storage, ingestion, transformation, serving, plus the undercurrents. See `references/data-engineering-lifecycle.md`.
6. **Design the layers, integration and model.** `references/modeling-and-layers.md`, `references/inmon-enterprise-data.md` and `references/dimensional-modeling.md`. Name the system of record for every core entity. For source specifics such as Oracle Fusion HCM, see `references/oracle-fusion-hcm.md`.
7. **Map to the platform.** Fabric: `references/fabric-patterns.md`. Databricks: `references/databricks-unity-catalog.md`. Foundry: start with `references/palantir-foundry.md`, then `foundry-ontology-design.md`, `foundry-pipelines-security.md` and `foundry-aip.md`. For others, apply the same principles to the equivalent features.
8. **Stress test the design** against the foundations checklist: replication, partitioning, consistency, failure, schema evolution, idempotency.
9. **Plan trust, security and AI.** Governance (`governance.md`, `dmbok-data-management.md`), quality (`data-quality-observability.md`), security (`security-sovereignty.md`), AI readiness and ML (`ai-ready-data.md`, `mlops-llm-platforms.md`).
10. **Check against enterprise standards.** Does the design comply with existing architecture and technology standards? If it departs from one, record an exception with a reason and an expiry.
11. **Record decisions.** One ADR per significant decision using `templates/adr.md`, registered per `references/decision-framework.md`.
12. **State risks and what would change the decision.** Every recommendation ends with the conditions under which you would choose differently.

## Core workflow for the architecture practice

When the request is about the practice rather than a single system (standards, review boards, EA strategy, operating model):

1. Run an assessment with `templates/ea-assessment.md` (outcomes, capabilities, gaps).
2. Score the practice against the four strategy objectives in `references/enterprise-architecture.md`.
3. Propose improvements using the enable before enforce principle and the manifesto values.
4. Set up or repair the decision process with `references/decision-framework.md`.

## Guiding principles

**From the data architecture sources**
* **Requirements before tools.** A technology chosen before the questions are answered is a guess.
* **Tradeoffs, not best practices.** Consolidation buys cost efficiency and simplicity; isolation buys predictability and autonomy. Say which you are buying.
* **Fix meaning once, in a reusable layer.** Business definitions belong in a curated layer or semantic model, not repeated in every report, prompt or agent.
* **Reports for truth, GenAI for exploration.** When an answer must be repeatable and auditable, use a governed report. Use agents and chat for discovery around the edges.
* **Copy only when it earns its place.** Prefer zero copy access when performance, availability, residency and control requirements are met.
* **Security is enforced, never described.** A description or prompt saying "confidential" is not access control.
* **Governance is an operating model, not a product.**

**From the systems and engineering sources**
* **Numbers or it is not a requirement.** Percentile latency, load, RPO and RTO, growth.
* **One system of record per entity.** Everything else is derived and rebuildable.
* **No dual writes.** Use change data capture or an outbox.
* **Idempotent and replayable.** Any job can be rerun safely; raw inputs are kept.
* **Design for partial failure.** Networks, clocks and nodes fail; plan for it.
* **Prefer reversible decisions.** Spend scrutiny on the irreversible ones.
* **Cost is an architectural property.** Make it visible per workload.
* **Model actions, not just data.** In an ontology, the verbs (actions, workflows) are where operational value lives.

**From the enterprise architecture source**
* **Context over silos.** Decide with the whole capability landscape in view, not just the project in front of you.
* **Tangible direction over stale documents.** Prefer reference implementations, golden paths, templates and docs as code over long static documents.
* **Drive behaviour, do not just police it.** Make the right way the easy way first; enforce only what genuinely needs enforcing.
* **Evolve, do not worship frameworks.** Use frameworks as inputs; adapt them to the organisation and revisit them as it changes.
* **Decide, record, commit.** Once a decision is made through the agreed process, teams commit to it even if they disagreed. Reopen only with new information.

## Reviewing an existing codebase or design

1. Inventory sources, ingestion, storage layers, transformation code, semantic models and consumers. Draw the current state before judging it.
2. Check each layer against `templates/architecture-review.md`.
3. Flag findings as **Critical** (data loss, security exposure, wrong numbers), **Major** (scaling, cost, maintainability, standard violations) or **Minor** (naming, documentation).
4. For every finding give the evidence (file and line, or component), the impact, and a concrete fix.
5. Note any finding that should become an enterprise standard or a registered decision.

## Output conventions

* Lead with the recommendation, then the reasoning, then the alternatives considered.
* Use tables for comparisons and Mermaid diagrams for flows, layers and capability maps.
* Name the pattern explicitly (for example "lakehouse with a domain oriented serving layer"), not just a product list.
* Tie each recommendation back to a business outcome and a capability.
* Avoid em dashes. Prefer short declarative sentences in a direct, professional tone.

## Reference files

| File | Read when |
| --- | --- |
| `references/design-session.md` | Starting any new design or engagement |
| `references/architectures.md` | Choosing or comparing MDW, fabric, lakehouse, mesh |
| `references/data-systems-foundations.md` | Storage, replication, partitioning, consistency, streaming, failure |
| `references/data-engineering-lifecycle.md` | End to end pipeline design, undercurrents, technology choice |
| `references/modeling-and-layers.md` | Designing zones, models, ETL or ELT, semantic layer |
| `references/inmon-enterprise-data.md` | Enterprise integration, system of record, Data Vault, unstructured text |
| `references/fabric-patterns.md` | Anything on Microsoft Fabric, OneLake, Power BI capacity |
| `references/palantir-foundry.md` | Foundry overview, ontology strategy, self service tools, IoT and time series |
| `references/foundry-ontology-design.md` | Ontology principles, structural guidance, anti patterns, actions, Automate, OSDK, validation |
| `references/foundry-pipelines-security.md` | Project structure, Data Connection, transforms, branching and release, markings and policies |
| `references/foundry-aip.md` | AIP Logic, Chatbot Studio, Evals, AI design patterns, staged writes, AIP governance |
| `references/data-mesh.md` | Data products, contracts, mesh readiness, federated governance |
| `references/dmbok-data-management.md` | Data management functions, governance roles, MDM styles, maturity |
| `references/dimensional-modeling.md` | Star schemas, grain, SCDs, bus matrix, semantic model design |
| `references/data-quality-observability.md` | Tests, Great Expectations, freshness and volume monitoring, data incidents |
| `references/databricks-unity-catalog.md` | Databricks, Unity Catalog layout, ABAC, Delta Sharing, platform positioning |
| `references/security-sovereignty.md` | RBAC and ABAC design, multi entity RLS, privacy, sovereign and air gapped deployment |
| `references/mlops-llm-platforms.md` | ML lifecycle, LLM application architecture, evaluation, self hosted models |
| `references/oracle-fusion-hcm.md` | Extracting and modelling Oracle Fusion HCM data |
| `references/ai-ready-data.md` | Copilot, data agents, LLMs over enterprise data |
| `references/governance.md` | Catalog, lineage, ownership, quality, access |
| `references/enterprise-architecture.md` | Operating model, standards, strategy, alignment, assessment |
| `references/decision-framework.md` | Setting up decision records, registries and review boards |
| `templates/adr.md` | Recording a decision |
| `templates/architecture-review.md` | Reviewing an existing design or repo |
| `templates/ea-assessment.md` | Assessing an EA practice or a capability area |
