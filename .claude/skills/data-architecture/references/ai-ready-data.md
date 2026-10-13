# AI Ready Data

Based on James Serra's 2026 series on making data AI ready and his post on GenAI vs dashboards. Platform specifics refer to Microsoft Fabric; the principles apply equally to Foundry AIP, Databricks Genie or any LLM over enterprise data.

## Definition

AI ready data is accurate, well described, semantically clear, governed, accessible, and carries enough business context that an AI system can interpret it correctly. Accuracy is the starting point, not the finish line.

The test question: **what would a knowledgeable new employee need to be told to use this data correctly?** Everything on that list must be written down somewhere the AI can reach.

## Reports vs GenAI

* Reports and dashboards are deterministic: fixed logic, repeatable, auditable. Use them when an answer must be correct every time (KPIs, finance, performance against targets).
* GenAI is generative: wording, context and model affect the answer. Use it for exploration and discovery when the user does not yet know the question.
* GenAI widens the set of questions users can ask, so it widens the data you need to prepare. It usually creates more data work, not less.
* Users must be trained that a confident answer is not a correct answer, and told when to go back to the governed report.

## Preparation checklist

**1. Quality**
* Duplicates, missing values, inconsistent spellings, invalid dates, contradictions.
* Decide what makes an entity unique before deduplicating. Use master data management for core entities.
* Map code values (A, Active, Current) only after confirming they mean the same thing.
* Missing is not zero. Unknown and none are different facts.
* Document units, currencies, time zones, scale (units vs thousands), percentage representation, and which date a date column represents.

**2. Business language**
* Expand acronyms but keep both forms. Watch for overloaded abbreviations.
* Give technical names business friendly names and descriptions.
* State what a table actually contains, including filters (for example "one row per purchasing customer; excludes prospects").
* Add synonyms only where they really are interchangeable.
* Agree definitions for core metrics, including exclusions and time windows, with the business.

**3. Metadata**
* For each important asset: purpose, source system, owner, refresh frequency, limitations, grain (what one row represents), lineage, and whether it is authoritative or experimental.
* Document relationships, cardinality and hierarchies. Wrong joins silently multiply totals.

**4. Governance**
* Identify sensitive data, set ownership, and enforce access in the serving systems. A description saying "confidential" is not a control.

**5. Where to fix meaning**
* Do not rename production tables a legacy app depends on. Expose an AI friendly view or semantic model instead.
* Fix shared meaning once in a reusable layer (curated view, semantic model, ontology) rather than repeating it in every agent.
* Instructions guide interpretation; cleaning changes values; a semantic layer formalises meaning. An instruction cannot correct a wrong value.

**6. Documents**
* Clear titles and headings, owners, effective dates, current vs obsolete versions.
* When chunking for retrieval, keep the document title, section, link and context with each chunk.

**7. Test**
* Start with a small set of real business questions with agreed answers.
* Check the generated query (source, measure, filters, joins), not just the wording of the answer.
* Re test after every change to model, instructions or data.

## Fabric Data Agent: what goes where

The agent is an orchestrator: it picks the source and shapes the answer. For a Power BI semantic model it hands the question to a separate DAX generation tool, which does **not** see the agent instructions. It sees the model metadata and the model's Prep data for AI configuration.

| Put it in | What belongs there |
| --- | --- |
| **Agent instructions** | Which source to use for which topic, cross source rules, response format, shared terminology |
| **Semantic model metadata** | Clear names, descriptions, synonyms, relationships, measure definitions |
| **Prep data for AI: AI data schema** | Which tables, columns and measures AI should focus on (relevance, not security) |
| **Prep data for AI: AI instructions** | Model specific terminology and rules ("Revenue means Net Sales Amount") |
| **Prep data for AI: verified answers** | Approved visuals for common questions that guide query generation |
| **SQL or KQL source instructions, descriptions, example queries** | Joins, codes, calculation rules and example question to query pairs for non semantic model sources |

Rules of thumb:
* Agent instructions get you to the right source; model level preparation gets you the right query once there.
* Keep model knowledge with the model so Copilot and other experiences reuse it.
* Remove contradictions (for example AI instructions say Net Sales, a verified answer uses Gross Sales). Do not rely on precedence.
* Instructions are guidance, not enforcement.

## Equivalent concepts on other platforms

| Concept | Fabric | Palantir Foundry | Databricks |
| --- | --- | --- | --- |
| Business semantic layer | Semantic model, Fabric ontology | Ontology (object types, links, actions) | Unity Catalog metrics, metric views |
| Model level AI context | Prep data for AI | Object and property descriptions, AIP logic | Genie instructions, table comments |
| Orchestrator | Data Agent | AIP Agent Studio | Genie space, Agent Bricks |
| Governance | Purview, OneLake security | Markings, project permissions | Unity Catalog grants, ABAC |
