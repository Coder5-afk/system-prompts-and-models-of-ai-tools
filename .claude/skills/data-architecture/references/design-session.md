# Architecture Design Session (ADS)

Serra recommends starting every engagement with a structured discovery workshop before anyone draws technology boxes. The aim is to understand the business problem, the current state and the constraints well enough that the architecture almost chooses itself.

## Before the session

* Identify the executive sponsor and confirm the business outcome they care about.
* Collect existing artefacts: architecture diagrams, source system list, report inventory, data volumes, licensing, existing architecture standards and prior decisions.
* Invite the right mix: business owners, data producers, data consumers, security, infrastructure, enterprise architecture, and the people who will operate the platform.

## Agenda

1. **Business outcomes.** What decisions will this data support? What does success look like in 12 months? What does it cost not to do it?
2. **Capabilities.** Which business and technical capabilities are needed to deliver those outcomes? Which exist today, and how mature are they?
3. **Current state.** Sources, ingestion, storage, reporting tools, pain points. Draw it as it really is, including spreadsheets and manual steps.
4. **Requirements discovery** (questions below).
5. **Future state options.** Sketch two or three candidate patterns, never just one.
6. **Tradeoffs and decision.** Compare candidates against the requirements. Capture each decision as an ADR.
7. **Next steps.** Proof of concept scope, owners, dates.

## Discovery questions

**Data**
* How many source systems? Operational databases, SaaS, files, streams, documents?
* Volumes today and in three years? Growth rate?
* Structured, semi structured, unstructured? Images, audio, PDFs?
* What latency does each use case actually need: daily, hourly, near real time, real time?
* Is there a master data problem (the same customer, employee or entity in several systems)?

**Consumers**
* Who consumes the data: executives, analysts, data scientists, applications, agents, external parties?
* Which questions are known and recurring (reports), and which are exploratory (self service, AI)?
* Concurrent users and peak periods?

**Organisation**
* Is data ownership centralised, federated or decentralised?
* Do business domains have their own engineering skills?
* What skills exist today: SQL, Spark, Python, DAX, low code?
* Who owns and operates the platform after go live?
* Which enterprise standards and prior decisions already constrain the design?

**Constraints**
* Regulatory, residency and sovereignty requirements? Air gapped or disconnected needs?
* Security model: RBAC, ABAC, row and column level, classification?
* Budget model: capex vs opex, reserved vs pay as you go, chargeback or showback?
* Existing licences and strategic vendor commitments?

**Quality and trust**
* Which numbers are currently disputed between teams?
* Are there agreed definitions for the core KPIs?
* How is data quality measured today, if at all?

## Output of the session

* One page problem statement linked to business outcomes
* Capability map with current maturity and gaps
* Current state diagram
* Prioritised requirements (must, should, could)
* Two or three candidate architectures with tradeoffs
* Decisions captured as ADRs
* Explicit list of open questions and assumptions
