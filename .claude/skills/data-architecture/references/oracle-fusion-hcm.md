# Oracle Fusion HCM as a Source System

Drawn from general Oracle Fusion Cloud practice. Oracle updates Fusion quarterly; confirm current extraction options, limits and object names in Oracle documentation and your pod's release.

## Extraction options

| Option | What it is | Best for | Watch for |
| --- | --- | --- | --- |
| **BI Cloud Connector (BICC)** | Extracts view objects (PVOs) to Object Storage or UCM as CSV, full or incremental | Bulk analytical extraction into a lake or lakehouse | Choose extract PVOs over BI PVOs where available; incremental relies on last update dates; deletes need separate handling |
| **HCM Extracts** | HCM specific extract definitions with delivery options | Effective dated HR data, payroll and HR outbound interfaces | Complex to maintain; performance on large populations |
| **BI Publisher / OTBI reports** | Report based extraction | Small, specific datasets | Not designed for bulk or high frequency extraction; row limits |
| **REST APIs** | HCM REST resources | Near real time integration, small lookups, writeback | Paging, throttling, not for bulk history |
| **Atom feeds** | Change events for workers and assignments | Event driven updates to downstream systems | Event coverage is limited to supported objects |
| **Oracle Fusion Data Intelligence (FDI)** | Oracle's packaged analytics with prebuilt pipelines into Autonomous Data Warehouse | Fast prebuilt HCM analytics | Oracle centred; integrating with a non Oracle lakehouse adds a copy |
| **HCM Data Loader (HDL)** | Bulk inbound loading | Loading data into Fusion | Inbound, not extraction |

Typical enterprise pattern: BICC for bulk analytical extracts into the lakehouse or Foundry, REST or Atom feeds for operational events, FDI only if Oracle packaged analytics are part of the reporting strategy.

## Modelling HCM data

* **Effective dating:** most core HR objects are date effective (`EFFECTIVE_START_DATE`, `EFFECTIVE_END_DATE`) with multiple changes per day possible (`EFFECTIVE_SEQUENCE`, latest change flags). Model these as Type 2 dimensions or point in time snapshots; never take "current row" naively.
* **Core keys:** person (`PERSON_ID`), assignment (`ASSIGNMENT_ID`), work relationship (period of service), legal employer, business unit, department, job, position, grade, location. Understand the person, work relationship, assignment hierarchy before modelling.
* **Multiple assignments:** one person can hold several assignments; decide the primary assignment rule for headcount.
* **Legislative data groups:** payroll and some HR data vary by legislation; keep LDG as an attribute.
* **Flexfields:** descriptive and extensible flexfields carry entity specific attributes. Document each one's meaning per entity; they differ across implementations.
* **Lookups:** coded values come from lookup types; join to lookups for meanings and keep them as reference data.
* **Deleted and purged records:** design detection for deletions (BICC primary key extracts or full reconciliation).

## Multi entity considerations

When many entities share one Fusion instance or several instances:
* Use legal employer and business unit to derive the entity key for row level security.
* Expect configuration differences (flexfields, lookups, grades, job architecture) across entities; conform them in the silver layer with mapping tables owned by data stewards.
* Separate global definitions (headcount, FTE, turnover) from entity specific ones and document both.

## Data quality hotspots

* Missing or wrong manager assignments break hierarchies.
* Overlapping effective dated rows from data migration.
* Inconsistent job and position codes across entities.
* Free text fields used instead of lookups.
* Duplicate person records from rehires or migration.
Run expectation suites on these at the silver layer and feed issues back to HR operations.

## Custom extensions around Fusion

Many organisations hold custom applications (for example Oracle APEX) next to Fusion for processes Fusion does not cover. Treat each as a separate source system with its own owner, contract and extraction method; do not let it bypass the governed pipeline.

## Reference pipeline

```
Fusion HCM
  BICC extract PVOs (incremental + PK for deletes) -> object storage
  Atom feeds / REST (events)                      -> streaming or API ingest
        |
Raw:        files as delivered, by extract date
Conformed:  Delta tables, typed
Silver:     effective dated history, entity conformed, lookups resolved, quality checked
Gold:       employee, assignment, organisation dimensions (SCD2); headcount, movement, absence, payroll facts
Semantic:   certified HR model / ontology objects (Employee, Position, Organisation, Entity)
```
