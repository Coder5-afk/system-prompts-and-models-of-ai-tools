# Foundry Ontology Design

Based on Palantir's official Foundry documentation (palantir.com/docs/foundry): Ontology overview, design best practices, structural guidance, anti patterns, action types, Automate and the Ontology SDK. Summarised in our own words as of October 2026. Check the docs for current feature support.

## Building blocks

| Element | Layer | Purpose |
| --- | --- | --- |
| Object type | Semantic | A real world entity, backed by datasets or virtual tables |
| Property | Semantic | A fact about the entity, with metadata and security |
| Struct property | Semantic | Related fields used together (address), or a value plus metadata (source, confidence, timestamp) |
| Derived property | Semantic | Computed at runtime from linked objects or aggregations |
| Link type | Semantic | A real relationship between object types |
| Object backed link | Semantic | A relationship that has its own attributes (role, dates, allocation) |
| Interface | Semantic | A shared shape or capability implemented by several object types; enables polymorphism |
| Action type | Kinetic | A governed transaction that edits objects, properties and links, with validation and side effects |
| Function | Kinetic | Business logic (TypeScript or Python) for derived values, actions and applications |
| Automation | Kinetic | Conditions (schedule or object data) that trigger effects (actions, functions, AIP Logic, notifications) |
| Security policies | Dynamic security | Object and property level row and column controls |

## Four design principles, in priority order

When principles conflict, the higher one wins.

### 1. Domain driven design: model reality, not systems
* Object types are real concepts (Employee, Position, Case, Facility), not tables, API responses or spreadsheet tabs.
* Identify entities with domain experts **before** looking at source schemas, then map data into the model.
* Separate identity from observation: a measurement, event or assessment about an entity is usually its own object type.
* Name for humans (`employee.lastPromotionDate`, not `dtLstPrmMod`).
* Hide purely technical types from default views.

### 2. Do not repeat yourself (rule of three)
* One canonical type per concept and one canonical workflow per operation.
* One duplicate is tolerable, two is a warning, three means refactor.
* Resolve team copies with one shared type plus security, or with a shared interface.

### 3. Open for extension, closed for modification
* Once a core type, interface or action is in production, keep its core structure stable.
* Add new needs by extension: a linked type, a new interface implementation, or a property namespace.
* Example: add an `Employee Certification` linked type and a `Certifiable` interface rather than adding certification columns to `Employee`.
* Make sure extensions cannot widen data access.

### 4. Composition over deep hierarchies
* Use capability interfaces (`Schedulable`, `Inspectable`, `Approvable`, `Billable`) and taxonomic interfaces (an `Asset` category) instead of inheritance chains.
* Build actions, functions and apps against interfaces so one workflow serves many types.

**Pragmatism:** do not block delivery for purity. Name tradeoffs explicitly, improve incrementally, and never compromise on naming quality, semantic clarity or security design, because they are expensive to fix later.

## Structural guidance

**Properties**
* Store each fact once, on the object where it belongs.
* Precompute simple same object values that change only through pipelines.
* Use derived properties for values depending on links or action edits (for example a manager's direct report count).
* Denormalise only deliberately, usually at large scale, and document the source of truth and sync method.

**Structs**
* Use for multi field values used together, and to attach provenance to AI generated values (source, confidence, model, timestamp).
* Set a main field so the struct behaves like a simple property.

**Links**
* Every link answers a real domain question; a shared foreign key alone is not a reason.
* Use an object backed link when the relationship has attributes.
* Name links in both directions (an employee's department, a department's employees).

**Naming**
* Object types: singular, concrete nouns a domain expert recognises.
* Properties: no type prefixes or implementation details; qualify ambiguous words (`contractValue`, not `value`).
* One date naming convention across the ontology.
* Agree conventions before building; review names with end users.

**Security**
* One object type with row and column policies, not duplicate types per audience.
* Start restrictive and open deliberately.
* Align security boundaries with domain boundaries, enforced by policy rather than application filters.
* Check that every new link, type or property preserves existing protections.

## Anti patterns

| Anti pattern | Symptom | Fix |
| --- | --- | --- |
| System silos | One object type per source system for the same entity | One type backed by a merged dataset with precedence rules |
| Kitchen sink | Every source column imported, including pipeline metadata | Keep business meaningful properties; leave technical fields in the dataset |
| Department silos | Each department builds its own version of a shared entity | Shared type; department detail as properties or linked types; restricted views for access |
| God object | One type for many entities with sparse, shifting properties | Distinct types; interfaces for genuinely shared traits |
| Golden hammer | One tool for everything | Pipelines for processing, actions for human edits, automations for events, functions for real time logic |
| Action sprawl | Many tiny actions each changing one property | Actions designed around business operations |
| Time machine | v1, v2, v3 objects or one type per year | One object per entity; history via linked amendment objects, time series or edit history |
| Misnomer | Names like `value`, `type`, `date`, `Item` | Specific names, qualified properties, descriptions |

## Action types

* An action is a single governed transaction that changes one or more objects, properties and links according to defined logic.
* Components: **parameters** (structured inputs), **rules** (the edits to make, such as creating links or setting values), **submission criteria** (who can run it and when it is valid), **side effects** (notifications, webhooks to external systems), and function backed logic for complex cases.
* The same action and validation apply in every application, so edits stay consistent.
* Edits land in the object type's writeback dataset and are logged.
* Design guidance: one action per business operation (for example "Transfer employee" sets the new position, links the new manager, notifies both managers and records the effective date), not one action per field.

## Choosing the right mechanism

| Need | Use |
| --- | --- |
| Batch transformation of source data | Pipeline (Pipeline Builder or Code Repository) |
| Human or agent decision that changes state | Action type |
| React to a data condition or schedule | Automate |
| Real time computed value or complex logic | Function |
| LLM reasoning over objects | AIP Logic (see `foundry-aip.md`) |
| External application reading or writing objects | Ontology SDK |

## Automate

* **Conditions:** time based, object data based (for example a count crossing a threshold or new objects entering a set), or both.
* **Effects:** run actions, run functions or AIP Logic, send platform or email notifications with attachments.
* Use cases: scheduled digests, data alerts, workflow automation, watched searches.
* Document each automation's purpose, data scope, execution identity and potential impact.
* For consequential changes, **stage edits for human review** rather than applying them directly, and provide a recovery path.
* Build and test automations on a branch before merging.

## Ontology SDK (OSDK)

* Generates type safe clients (TypeScript, Python, Java, or others via OpenAPI) for the slice of the ontology an application needs.
* Used for custom React front ends, external services and containerised compute modules.
* Each application uses a token scoped to the entities it needs, intersected with the user's permissions.
* Read time row and column controls do not protect data once it leaves the platform; pair them with markings or classification based controls for downstream protection.

## Validation before release

* Run task based exercises: give real users and agents real business questions and check they can navigate the ontology to answer them and take the right actions.
* Check names, descriptions and link directions with domain experts.
* Confirm security with persona based test cases.
* Review against the anti pattern table.

## HR and government example

| Object type | Key links | Typical actions |
| --- | --- | --- |
| Person | has Assignments, belongs to Entity | Update personal details |
| Assignment | Person, Position, Organisation Unit, Manager | Transfer, Promote, Terminate |
| Position | Organisation Unit, Job, Grade | Create, Freeze, Reclassify |
| Organisation Unit | parent Organisation Unit, Entity | Restructure |
| Entity | Organisation Units | (reference) |
| KPI | Entity, Owner, Measurements | Submit value, Approve, Comment |
| Eligibility Case | Person, Rule Set, Decision | Assess, Approve, Appeal |

Use interfaces such as `Approvable` (KPI, Eligibility Case, Position change) to reuse one approval workflow across object types.
