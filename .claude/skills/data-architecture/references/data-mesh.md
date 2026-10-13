# Data Mesh in Depth

Distilled from the concepts in **Zhamak Dehghani, *Data Mesh: Delivering Data-Driven Value at Scale* (O'Reilly)**. Summarised in our own words. Read with `architectures.md`, which covers when mesh fits at all.

## The four principles

### 1. Domain ownership
* Analytical data is owned by the business domain closest to it: the source aligned domain that produces it, or the consumer aligned domain that shapes it for a use.
* Ownership means accountability for quality, definitions, availability and change, not just hosting.
* Three kinds of domain data: **source aligned** (facts from operational systems), **aggregate** (combines several sources for a wider view), **consumer aligned** (shaped for a specific use).

### 2. Data as a product
A data product is the smallest independently deployable unit of the architecture. It bundles data, code, metadata, policies and infrastructure. It must be:

| Quality | Meaning |
| --- | --- |
| Discoverable | Listed in a catalog with a clear description |
| Addressable | Has a stable, unique address |
| Understandable | Semantics, schema, examples and grain documented |
| Trustworthy | Published SLOs for freshness, completeness, accuracy; lineage visible |
| Natively accessible | Served in forms consumers need (SQL, files, API, events) |
| Interoperable | Uses shared identifiers and standards so it can be joined with others |
| Valuable on its own | Useful without stitching together many other products |
| Secure | Access policies enforced and travel with the product |

Each product has a **data product owner** (accountable for value and roadmap) and engineers.

### 3. Self serve data platform
A platform team provides capabilities so domains can build products without deep infrastructure skills. Dehghani describes three planes:
* **Infrastructure utility plane:** storage, compute, identity, networking.
* **Data product experience plane:** create, build, test, deploy, version and retire products with templates.
* **Mesh experience plane:** discover, search, link and govern across products.

### 4. Federated computational governance
* A federated team of domain representatives and platform and governance specialists sets global policies.
* Policies are **computational**: embedded and enforced automatically by the platform (classification, access, retention, interoperability checks), not by manual review.
* Decide globally only what must be global (identity, interoperability, security, privacy). Leave the rest to domains.

## Data contracts

Contracts make product interfaces explicit:
* Schema and semantics, with versioning rules
* SLOs (freshness, completeness, availability)
* Ownership and support channel
* Allowed uses and access policy
* Deprecation policy and notice period
Automate contract checks in the producer's CI so breaking changes fail before release.

## Readiness assessment

Do not start a mesh unless most of these are true:
- [ ] The organisation is large and genuinely decentralised in how it runs
- [ ] Domains have, or can fund, their own data engineering capacity
- [ ] A central team is a proven bottleneck
- [ ] There is a capable platform team
- [ ] Executive sponsorship for an operating model change exists
- [ ] There is appetite for product management of data

## Incremental adoption

1. Introduce data product thinking (owners, SLOs, catalog) on a central platform.
2. Publish shared identifiers and interoperability standards.
3. Build platform templates for creating products.
4. Move ownership of a few willing, capable domains first.
5. Automate governance policies as the number of domains grows.

## Common failure modes

* Mesh "in name only": central team renamed, nothing else changes
* Every domain builds its own platform
* No shared identifiers, so products cannot be joined
* Governance remains manual and becomes the new bottleneck
* Products that are just raw table dumps with no SLOs or semantics

## Multi entity and government context

In a federation of many entities sharing a platform, mesh principles map naturally: each entity owns its source aligned products, a central body owns shared identifiers and reference data, and the platform enforces common security and interoperability. Weigh this against entity capability; many entities will need a managed service option rather than full self service.
