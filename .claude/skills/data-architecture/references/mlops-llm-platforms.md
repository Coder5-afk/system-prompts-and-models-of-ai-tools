# MLOps and LLM Platform Architecture

Drawn from general MLOps and LLMOps practice (including Chip Huyen's *Designing Machine Learning Systems* and *AI Engineering*, O'Reilly). Summarised in our own words. Tooling changes fast; verify current capabilities.

## Lifecycle

`data > features > training or adaptation > evaluation > registry > deployment > monitoring > retraining`

Every stage needs versioning, lineage and an owner.

## Classical ML components

| Component | Purpose |
| --- | --- |
| Feature store | Consistent feature definitions for training and serving; avoids training serving skew |
| Experiment tracking | Parameters, metrics, artefacts per run (MLflow or equivalent) |
| Model registry | Versioned models with stage (staging, production), approvals and lineage |
| Serving | Batch scoring, real time endpoints, or streaming |
| Monitoring | Data drift, prediction drift, performance against ground truth, latency and cost |

Practices:
* Point in time correct training data; no leakage from the future.
* Reproducible training from versioned data and code.
* Shadow or canary deployment before full rollout.
* Human review for high impact decisions (eligibility, scoring, enforcement).

## LLM application architecture

| Layer | Components |
| --- | --- |
| Models | Hosted APIs or self hosted open weight models; routing between them |
| Gateway | Authentication, rate limits, cost tracking, logging, guardrails, model routing |
| Retrieval | Document processing, chunking, embeddings, vector and hybrid search, reranking |
| Grounding in structured data | Semantic models, ontology, text to SQL or DAX with validated metadata |
| Orchestration and agents | Tools, actions, workflows, memory |
| Evaluation | Offline test sets, LLM as judge with human calibration, regression suites |
| Observability | Traces, prompts and responses, latency, cost, feedback |
| Safety | Input and output filtering, PII redaction, permission aware retrieval |

## Choosing an adaptation approach

| Approach | Use when |
| --- | --- |
| Prompting | Task is general; instructions and examples suffice |
| Retrieval augmented generation | Answers depend on enterprise knowledge that changes |
| Structured grounding (semantic model, ontology) | Answers depend on governed metrics and entities |
| Fine tuning | Consistent style, format or domain behaviour is needed and prompts fail |
| Small specialised models | High volume, narrow tasks where cost and latency matter |

Start with prompting and retrieval; fine tune only with evidence.

## Evaluation

* Build a test set of real questions with agreed answers before building the application.
* Evaluate retrieval (did we fetch the right content) separately from generation (did we answer correctly).
* For text to query, check the generated query, not just the prose.
* Run evaluations on every change to prompts, models, retrieval or data.
* Track groundedness, correctness, refusal appropriateness, latency and cost.

## Self hosted and open weight model platforms

* Size GPU capacity for concurrency and context length, not just model size.
* Use an inference server with batching and quantisation where acceptable.
* Separate environments for evaluation and production.
* Keep model weights, tokenizers and configurations under version control with checksums.
* Plan for model upgrades: run the evaluation suite before switching.

## Governance for AI

* Model inventory with owner, purpose, data used, risk tier.
* Documented intended use and limitations (model cards).
* Approval gates scaled to risk.
* Audit logging of prompts and outputs where required.
* Clear human accountability for decisions influenced by models.

## Platform mapping

| Capability | Fabric / Azure | Databricks | Foundry |
| --- | --- | --- | --- |
| Experiment and registry | Azure ML, MLflow in Fabric | MLflow, Unity Catalog models | Modeling objectives |
| Serving | Azure ML endpoints, AI Foundry | Model Serving | Model deployment, functions |
| Vector search | Azure AI Search | Vector Search | Semantic search in AIP |
| Agents | Fabric Data Agent, Copilot Studio | Agent Bricks, Genie | AIP Agent Studio, AIP Logic |
| Evaluation | AI Foundry evaluation | Agent Evaluation | AIP Evals |
