# Security Architecture and Sovereign Deployment

Drawn from general security architecture practice (zero trust, NIST and ISO 27001 style controls) applied to data platforms. Map controls to the national and sector frameworks that apply to you, for example UAE Information Assurance Standards and Abu Dhabi data management and cloud policies. Verify current regulatory requirements with your security and legal teams.

## Principles

* **Zero trust:** verify every request, least privilege, assume breach.
* **Defence in depth:** identity, network, data, application and monitoring layers.
* **Enforce at the data layer:** controls travel with the data, not with the application.
* **Classify first:** you cannot protect what you have not classified.
* **Security in the platform:** paved paths make the secure way the default.

## Access control models

| Model | How it works | Strength | Weakness |
| --- | --- | --- | --- |
| **RBAC** | Permissions granted to roles; users get roles | Simple, auditable | Role explosion as conditions multiply |
| **ABAC** | Policies evaluate attributes of user, data, and context | Scales, expressive, follows tags | Harder to reason about and test |
| **ReBAC** | Access based on relationships (manager of, member of) | Natural for org hierarchies | Needs a relationship graph |
| **Purpose based** | Access tied to declared purpose | Privacy alignment | Requires process discipline |

Practical pattern: **RBAC for coarse access** (which workspaces, catalogs, projects) plus **ABAC for fine grained** rules (rows by entity, columns by sensitivity tag).

## Designing ABAC

1. Define a classification taxonomy (for example Public, Internal, Confidential, Secret; plus PII, health, financial tags).
2. Tag data at column and table level, automatically where possible.
3. Define user attributes from the identity provider (entity, department, clearance, role).
4. Write policies as rules: "users may see rows where row.entity = user.entity unless user.scope = cross_entity".
5. Test policies with a matrix of personas and expected results.
6. Audit policy changes like code changes.

Platform mapping: Unity Catalog governed tags and ABAC policies, Fabric OneLake security and Purview sensitivity labels, Foundry markings and granular policies.

## Multi entity row level security

* Use a stable entity identifier on every relevant row.
* Map users to entities through a governed security table sourced from HR and identity, not manually maintained lists.
* Handle cross entity roles (central oversight, auditors) explicitly.
* Apply the same rule in every engine; prefer one central enforcement point.

## Privacy

* Data minimisation: collect and keep only what the purpose needs.
* Pseudonymise or tokenise identifiers in analytical layers where full identity is not needed.
* Retention schedules enforced automatically.
* Record purpose and legal basis for sensitive datasets.

## Sovereign and air gapped deployment

**Decisions to make explicitly**
* Data residency: where data at rest, backups, logs and telemetry live.
* Operational sovereignty: who can administer the platform, from where, with what clearance.
* Software supply chain: how updates enter, how they are verified, who approves.
* Key management: customer managed keys, hardware security modules held in country.
* Connectivity: connected, restricted egress, or fully disconnected.

**Air gapped patterns**
* Mirror required package registries, container images and model weights inside the boundary; scan and sign them on entry.
* One way transfer (data diode or controlled transfer zone) for updates.
* Local identity provider and certificate authority.
* Local monitoring and SIEM; no vendor telemetry leaving the boundary.
* Plan model and software update cadence; air gaps make patching slower, so compensate with stronger isolation.

**Sovereign AI specifics**
* Run open weight models on in country infrastructure; keep prompts, outputs and fine tuning data inside the boundary.
* Log and retain prompts and responses for audit under the same classification as the data used.
* Apply the same access controls to retrieval (RAG) indexes as to the source data; vector stores often leak data otherwise.

## Security review checklist

- [ ] Classification scheme applied to all curated data
- [ ] Access enforced at data layer with RBAC plus ABAC
- [ ] Groups from identity provider; no individual grants
- [ ] Secrets in a vault; no credentials in code
- [ ] Encryption at rest and in transit; key ownership defined
- [ ] Private networking for data services
- [ ] Audit logs retained and monitored
- [ ] Residency and sovereignty requirements documented and met
- [ ] AI retrieval indexes respect source permissions
- [ ] Policies tested with persona matrices
