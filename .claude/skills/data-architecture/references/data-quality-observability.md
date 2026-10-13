# Data Quality, Testing and Observability

Drawn from general data engineering practice (Reis and Housley's DataOps undercurrent, DMBOK data quality, and the Great Expectations and dbt testing approaches). Tool details change; check current documentation.

## Three layers of assurance

| Layer | Question | Examples |
| --- | --- | --- |
| **Testing** | Is the code correct before release? | Unit tests on transforms, contract tests, schema checks in CI |
| **Validation** | Does this batch or stream meet expectations? | Not null, unique, accepted values, ranges, referential integrity, row count bounds |
| **Observability** | Is the system healthy over time? | Freshness, volume, schema drift, distribution drift, lineage based impact |

## Where to check

* **At ingestion:** schema, row counts vs source, duplicates, file arrival.
* **Between layers:** reconciliation of counts and totals, referential integrity to master data.
* **At gold and semantic layer:** business rules, KPI sanity ranges, reconciliation to source of truth figures.
* **At consumption:** report level checks for known totals.

## Great Expectations pattern

* Define **expectation suites** per critical dataset (for example `expect_column_values_to_not_be_null`, `expect_column_values_to_be_in_set`, `expect_table_row_count_to_be_between`).
* Run them as **checkpoints** inside the pipeline after each load.
* Publish results as data docs and send failures to alerting.
* Decide per rule whether a failure **blocks** the pipeline (quarantine data) or **warns** (publish with a flag).
* Version suites in source control alongside the transformation code.

Equivalents: dbt tests, Fabric data quality rules and Purview data quality, Foundry Data Health and expectations, Databricks Lakehouse Monitoring and DLT expectations.

## Freshness, volume and schema monitoring

* Every gold asset has a freshness SLO (for example "updated by 06:00 local daily").
* Track row counts per load and alert on deviation from a rolling baseline.
* Detect schema changes at ingestion and fail or alert before they hit consumers.
* Track distribution drift on key columns used by ML or AI.

## Incident management for data

1. Detect (monitor or user report).
2. Triage using lineage: which downstream products and reports are affected?
3. Communicate to affected consumers with an estimate.
4. Fix at the source of the error, not in a downstream patch.
5. Backfill or replay using idempotent jobs.
6. Post incident review and a new test so it cannot recur silently.

## Quality scorecards

* Measure the DMBOK dimensions (completeness, uniqueness, validity, accuracy, consistency, timeliness) for critical data elements.
* Show scores in the catalog next to the asset so consumers see trust levels.
* Report trends to data owners; quality is their accountability.

## Minimum standard for a curated asset

- [ ] Primary key uniqueness and not null tests
- [ ] Referential integrity to conformed dimensions
- [ ] Accepted values for coded fields
- [ ] Row count and freshness checks with alerts
- [ ] Reconciliation to source totals for key measures
- [ ] Owner notified on failure
- [ ] Tests in CI and run on every load
