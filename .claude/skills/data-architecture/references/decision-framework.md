# Architecture Decision Framework

Based on the decision making framework in **Tanu McCabe, *Fundamentals of Enterprise Architecture***, combined with standard architecture decision record (ADR) practice. Summarised in our own words.

## What counts as an architecture decision

A choice that is significant, hard to reverse, affects more than one team, or sets a precedent. Examples: adopting a platform, choosing a lake layer convention, picking a security model, deciding centralised vs federated ownership, accepting an exception to a standard.

Not every choice needs an ADR. Over recording creates noise; under recording leads to relitigation.

## Components

### 1. Decision foundation
* Agreed principles the decision will be tested against (for example "open formats by default", "security enforced at the data layer", "buy before build for commodity capabilities").
* Clear decision rights: who proposes, who is consulted, who decides, who is informed.
* Criteria for which decisions need which level of review.

### 2. Decision registry
* One searchable place for all decisions, ideally versioned alongside code (a `docs/adr/` folder per repo plus an enterprise index).
* Each decision has an ID, status, owner, date, scope and links to related decisions.
* Engineers must be able to find relevant decisions in under a minute, or they will ignore them.

### 3. Decision lifecycle
Statuses: **Proposed, Under review, Accepted, Rejected, Superseded, Deprecated.**
* Decisions are never deleted. Superseded decisions link to their replacement.
* Each decision has a review date or trigger condition.

### 4. Workflow

| Decision tier | Example | Process |
| --- | --- | --- |
| Team | Library choice inside one service | Team records ADR, no review |
| Domain | Pipeline pattern for a domain | Domain architect reviews |
| Enterprise | Platform, standard, cross domain model | Architecture review board or design authority |

* Timebox decisions. An undecided question is itself a decision with costs.
* Once decided through the agreed process, teams commit. Reopen only when new information appears, not because someone missed the meeting.

### 5. Training
* Teach teams how to write a decision, when one is needed, and where to find existing ones.
* Provide the template and good examples.

### 6. Monitor execution
* Track whether accepted decisions are actually implemented.
* Automate checks where possible (policy as code, pipeline linting, IaC rules).
* Track exceptions with owners and expiry dates.

### 7. Evolve and enhance
* Review the framework regularly: time to decision, number of relitigated decisions, exception volume, team satisfaction.
* Simplify where the process is adding friction without adding value.

## Writing a good decision

* State the context and the forces at play, including constraints and business outcomes.
* List at least two real alternatives with their tradeoffs. "Do nothing" is often a valid alternative.
* Make the decision explicit in one sentence.
* Record consequences, both positive and negative, and follow up actions.
* State what would cause you to revisit the decision.

Use `templates/adr.md`.
