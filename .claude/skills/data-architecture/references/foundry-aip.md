# Palantir AIP Architecture

Based on Palantir's official documentation (palantir.com/docs/foundry): AIP overview, AIP Logic, AIP Chatbot Studio (formerly Agent Studio), AIP Evals, Automate and the Ontology SDK. Summarised in our own words as of October 2026. Names and capabilities change often; check the docs.

## Components

| Component | Purpose |
| --- | --- |
| **AIP Logic** | No code environment to build, test and release LLM powered functions. Inputs are objects or text; outputs are objects, text, or ontology edits |
| **AIP Chatbot Studio** | Builds interactive assistants grounded in the ontology, documents and custom tools, with application state as context |
| **AIP Evals** | Test suites for Logic functions and chatbots; groups failures by root cause and suggests prompt improvements |
| **Automate** | Runs Logic functions and actions on schedules or data conditions |
| **AIP Assist** | In platform help for users and builders |
| **Model catalogue** | Choice of hosted, bring your own and self hosted language models, with capacity management |
| **Ontology SDK and platform APIs** | Expose chatbots and functions to external applications |

## Why the ontology matters for AI

* LLMs read objects, properties and links with business names and descriptions instead of raw tables, so the ontology is the grounding layer.
* LLMs act through **action types**, so every change goes through the same validation, permissions and audit as a human edit.
* Good ontology design (see `foundry-ontology-design.md`) is the single biggest factor in AIP answer quality, just as a well described semantic model is for Copilot.

## Design patterns

**1. Extraction and enrichment**
Logic function reads unstructured text (emails, case notes, documents), extracts structured values, and writes them as properties or linked objects. Store results in structs with source, confidence and model metadata.

**2. Triage and recommendation**
Automation watches an object set (new cases, alerts). Logic function classifies or prioritises each item and recommends an action. A human approves in a Workshop inbox.

**3. Staged writes**
For consequential changes, Logic or automation **stages** proposed edits for human review rather than applying them. Use this by default for anything affecting people, money or legal status.

**4. Conversational assistant over objects**
Chatbot grounded in a filtered object set from the current Workshop view, with tools to query objects, run functions and propose actions.

**5. Agent behind an external app**
Chatbot or Logic function exposed through the Ontology SDK to a custom front end, inheriting the user's permissions.

## Security

* AIP uses the same permission model as the rest of the platform. The LLM only reads what the running user is allowed to read.
* Read time row and column controls do not cover what the model writes or returns. Pair them with markings or classification controls so outputs remain protected downstream.
* Give each function and chatbot the narrowest tool and data scope it needs.
* Decide which model runs which workload according to data classification; keep sensitive workloads on approved, in boundary or self hosted models.
* Log sessions where audit is required.

## Evaluation and release

* Build an evaluation suite of real inputs with expected outputs before release (see `mlops-llm-platforms.md`).
* Use AIP Evals results to find failure clusters and fix prompts, tools or ontology descriptions.
* Develop on a branch, evaluate, review, then merge.
* Track success and failure counts, latency and cost in production.
* Re run evaluations when changing model, prompt, tools or ontology.

## Governance checklist for an AIP workflow

- [ ] Clear owner and documented purpose
- [ ] Data scope and execution identity documented
- [ ] Writes go through action types, staged for review where consequential
- [ ] Markings protect sensitive outputs
- [ ] Model choice matches data classification and sovereignty requirements
- [ ] Evaluation suite exists and passes
- [ ] Recovery path if the automation misbehaves
- [ ] Privacy and civil liberties impact considered for decisions about people
- [ ] Monitoring of usage, failures and cost
