# Enterprise Architecture Practice

Distilled from the concepts in **Tanu McCabe, *Fundamentals of Enterprise Architecture: Proven Frameworks for Effective Architecture Decisions* (O'Reilly, 2024)**. Summarised in our own words; read the book for the full frameworks, templates and case studies.

## Why enterprise architecture exists

Without it, organisations drift into silos, inconsistent technology choices and growing technical debt. EA sets a strategic direction for enterprise wide problems and provides the principles, standards and patterns that help delivery teams build reliable, secure, cost effective systems.

## Functions of the practice

| Function | What it does |
| --- | --- |
| **Strategy** | Sets direction, target architectures and principles |
| **Enablement** | Makes the right path easy: patterns, reference implementations, guidance, consultation |
| **Oversight** | Reviews, standards, exceptions and compliance |

## Architect roles

* **Enterprise architect:** cross organisation direction, capability view, standards.
* **Solution architect:** designs a solution across systems for a business need.
* **Application architect:** designs within a single application.
* **Specialist architects:** data, security, cloud, integration and similar domains.

## Organisational models

| Model | Strength | Risk |
| --- | --- | --- |
| Centralised | Consistency, clear authority | Bottleneck, distance from delivery teams |
| Federated | Close to delivery, domain expertise | Inconsistency, duplicated decisions |
| Hybrid | Central standards with embedded architects | Needs clear decision rights |

Pick the model that matches how the wider organisation makes decisions, and make decision rights explicit either way.

## Core deliverables

* **Architecture decisions** (recorded, with rationale and alternatives)
* **Patterns** (reusable, proven approaches to recurring problems)
* **Capability target architectures** (the future state for a business or technical capability)
* **Application target architectures** (the future state for a specific system)

## Four strategy objectives

McCabe structures an EA strategy around four objectives, expressed as OKRs. Use them to assess and improve a practice.

### 1. Shared alignment
* Align on **who**: identify stakeholders and how to engage them at scale.
* Align on **why**: sell the reason and agree the priority before arguing about the solution.
* Align on **the decision**: surface differing perspectives, use conflict productively, then commit.
* Choose the right decision style for the situation: disagree and commit, command and control, or consensus. Each has a cost; consensus on everything stalls, mandates without buy in get ignored.
* Antipatterns: the mandate nobody follows, endless relitigation of settled decisions, siloed decisions, debates that never end.

### 2. Embedded and accessible
* Architecture knowledge only matters if people find and use it in their normal workflow.
* Manage architecture knowledge through a lifecycle: create, approve, distribute, consume, retire.
* Apply UI and UX thinking to architecture content: design it for the reader, put it where engineers already work (repos, developer portals, templates, pipelines).
* Antipattern: the static artefact, a polished document nobody opens after publication.

### 3. Enable and enforce
* Standards cover architecture qualities (stability, release, operational efficiency, interoperability, security) and technology choices.
* Only declare a standard when it is needed, mature enough, and can be supported.
* **Enable first:** templates, golden paths, reference code, paved roads, self service.
* **Enforce selectively:** automated checks in pipelines, review gates for high risk changes, a clear exception process with expiry dates.
* Measure with KPIs (adoption, exceptions, time to decision, compliance).
* Antipatterns: the free for all (no standards), suffocation (too many standards, too much process), the reporter (EA that only reports non compliance without helping).

### 4. Proactive and reactive
* Balance planned strategic work with responding to the urgent.
* Strategic thinking: understand the right problem, initiate innovation, act as a change agent.
* The **4 Cs** for architects: curiosity, challenge, credibility, communication.
* Watch for inflection points (cloud migration, AI adoption) where proactive architecture creates outsized value, and for tactical overload where reactive work crowds out strategy.

## Manifesto values

McCabe proposes values in the style of the Agile manifesto. The right hand side still has value; the left hand side is valued more.

* **Contextual understanding** over siloed decision making
* **Tangible direction** over stale documentation
* **Driving behaviour** over enforcing standards
* **Evolution** over frameworks

What they mean in practice:
* **Context:** decide with an architectural domain model and capability map in view. Know the business outcomes, existing systems and prior decisions before deciding.
* **Tangible direction:** experiment, build proofs of concept and reference implementations. Treat documentation as code, versioned next to the systems it describes.
* **Driving behaviour:** understand what motivates teams and make the desired behaviour the easiest path, rather than relying on rules alone.
* **Evolution:** EA has moved from theory to data, from outsider to insider, from blocker to enabler. Frameworks such as TOGAF are inputs to adapt, not scripts to follow.

## Assessment framework

To assess a capability area or the EA practice itself:

1. **Define business outcomes.** What must improve, and how will it be measured?
2. **Identify capabilities** needed to deliver those outcomes.
3. **Map capability gaps** between current and target maturity.
4. **Prioritise** gaps by business impact and effort, and turn them into a roadmap.

Use `templates/ea-assessment.md`.

## Defining an EA strategy

Apply EA's own methods to itself:

1. **Contextualise:** tailor the practice to the organisation's outcomes, culture, structure and maturity.
2. **Continuously improve:** measure against the four objectives and revise regularly.
3. **Create excitement:** communicate wins, show value in business terms, build advocates.

## Applying this to data architecture

* A target data architecture is a capability target architecture. Tie it to business outcomes and a capability map.
* Data standards (layer conventions, naming, formats, security patterns) follow the enable before enforce rule: provide templates and reference pipelines first, automate checks second, review boards last.
* In federated or multi entity environments (many agencies, subsidiaries or domains), shared alignment and decision rights matter more than tooling. Decide centrally what must be common (identity, security, conformed data, interoperability standards) and let entities choose the rest.
