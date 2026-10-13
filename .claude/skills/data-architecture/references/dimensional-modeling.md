# Dimensional Modelling

Distilled from the concepts in **Ralph Kimball and Margy Ross, *The Data Warehouse Toolkit*, 3rd edition (Wiley)**. Summarised in our own words. Applies to gold layers, Power BI and SplashBI semantic models, and any reporting model.

## The four step design process

1. **Select the business process** (for example payroll run, leave request, recruitment, procurement), not the department.
2. **Declare the grain:** exactly what one fact row represents. Grain first, always.
3. **Identify the dimensions:** the who, what, where, when, why and how that describe each row.
4. **Identify the facts:** the numeric measurements true to the grain.

## Fact tables

| Type | Grain | Example |
| --- | --- | --- |
| **Transaction** | One row per event | Each payroll element posted |
| **Periodic snapshot** | One row per entity per period | Headcount per org unit per month end |
| **Accumulating snapshot** | One row per process instance, updated as milestones occur | Recruitment requisition from open to hired |
| **Factless** | Records an event or coverage with no measure | Training attendance, eligibility coverage |

Fact additivity:
* **Additive:** can be summed across all dimensions (amount).
* **Semi additive:** not summable across time (balances, headcount).
* **Non additive:** ratios and percentages; store numerator and denominator instead.

## Dimensions

* Use **surrogate keys** in dimensions; keep the natural key as an attribute.
* Make dimensions wide and descriptive; denormalise hierarchies into the dimension.
* **Conformed dimensions** (date, employee, organisation, entity, location) are shared across all fact tables. The **bus matrix** maps business processes to conformed dimensions and is the enterprise integration plan.
* **Date dimension:** always build one with fiscal calendars, holidays and working days.
* **Role playing dimensions:** one physical dimension used in several roles (hire date, termination date).
* **Junk dimensions:** combine low cardinality flags.
* **Degenerate dimensions:** transaction identifiers kept in the fact table.
* **Outriggers and bridges:** use bridges for many to many (employee to multiple cost centres) with allocation weights.

## Slowly changing dimensions

| Type | Behaviour | Use for |
| --- | --- | --- |
| 0 | Never changes | Original hire date |
| 1 | Overwrite | Corrections, attributes where history does not matter |
| 2 | New row with effective dates and current flag | Org unit, grade, job, location history |
| 3 | Previous value column | Limited "before and after" analysis |
| 6 | Hybrid of 1, 2 and 3 | History plus current value on every row |

HR data typically needs Type 2 on organisation, position, grade and job, with effective dated logic aligned to the source system.

## The bus matrix

| Business process | Date | Employee | Organisation | Position | Entity | Location |
| --- | --- | --- | --- | --- | --- | --- |
| Payroll | X | X | X | X | X | |
| Absence | X | X | X | | X | |
| Recruitment | X | | X | X | X | X |

Build conformed dimensions once and every process that uses them integrates automatically.

## Mapping to semantic models

* One star per business process; facts connect to conformed dimensions only through keys.
* Avoid snowflaking in the semantic layer; flatten hierarchies into dimensions.
* Avoid bidirectional relationships unless needed and understood.
* Define measures in the semantic model, not in visuals.
* Large models: aggregations or composite models over a detailed fact table.

## Anti patterns

* Mixed grain in one fact table
* Storing ratios instead of components
* Dimensions built separately per report
* Natural keys used as join keys across systems without conformance
* No date dimension
