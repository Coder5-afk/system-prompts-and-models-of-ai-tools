# Enterprise Data Integration (Inmon)

Distilled from the concepts in **W.H. Inmon, Daniel Linstedt and Mary Levins, *Data Architecture: A Primer for the Data Scientist* (Academic Press)**. Summarised in our own words. Inmon is the originator of the enterprise data warehouse; this book extends his thinking to big data, unstructured text and Data Vault.

## Core ideas

**The enterprise data warehouse**
* A subject oriented, integrated, time variant, non volatile collection of data supporting management decisions.
* Integration is the point: one corporate view of each entity, reconciled across source systems, with history.
* Departmental data marts are fed from the integrated warehouse, not built independently.

**Operational vs analytical**
* Operational systems serve current transactions. Analytical systems serve history and integration.
* An operational data store (ODS) can bridge them when near current integrated data is needed.

**System of record**
* For every data element, define the single most accurate and authoritative source. Integration starts from the system of record, not whichever extract is convenient.

**The end state architecture**
* An enterprise information architecture containing operational systems, integrated warehouse, marts, unstructured data processing, big data and metadata, all related through a common data model.

## Repetitive vs non repetitive data

Inmon separates big data into two kinds that need different handling:

| Type | Examples | Handling |
| --- | --- | --- |
| **Repetitive** | Sensor readings, logs, clickstream, meter data, transactions | Same structure every record; most records have little individual value; summarise, aggregate, and keep exceptions |
| **Non repetitive** | Emails, contracts, call transcripts, case notes, reports, medical records | Each record different; value is in the content; needs text processing to extract structure |

The design implication: do not apply one pipeline pattern to both. Repetitive data favours streaming, aggregation and downsampling. Non repetitive data favours text extraction and context capture.

## Textual disambiguation (textual ETL)

* Turning unstructured text into structured, analysable data requires **context**, not just keywords. The same word means different things in different documents and sentences.
* Steps include identifying the document type, applying taxonomies and ontologies, resolving homographs and acronyms, extracting entities and values, and linking results back to structured keys.
* Today this is done with NLP and LLMs, but the principle holds: preserve context, map to a taxonomy, and link to structured data.

## Data Vault

Developed by Linstedt and covered in the book as an integration modelling approach:
* **Hubs:** unique business keys (employee ID, entity code).
* **Links:** relationships between business keys.
* **Satellites:** descriptive attributes with full history and load metadata.
* Strengths: auditability, parallel loading, resilience to source change, full history.
* Usually sits in the integration layer, with dimensional marts or an ontology built on top for consumption.

## Data models

* A corporate data model (high level entities, mid level attributes, physical design) is the backbone of integration.
* Data models apply to structured data and, through taxonomies and ontologies, to text.

## Metadata

* Technical metadata (structure, lineage) and business metadata (definitions, owners) are both required.
* Metadata ties the end state architecture together and is what makes data usable by analysts and data scientists.

## Relevance to modern platforms

* Palantir Foundry's ontology is close to Inmon's integrated corporate model made operational: one integrated view of each entity, linked, with history and actions.
* Lakehouse silver layers often play the integrated warehouse role; Data Vault is a common way to model them.
* LLM based extraction is the modern form of textual disambiguation. The same rules apply: keep context, map to a governed taxonomy, and link to master data keys.

## Questions this lens adds to a review

* Is the system of record defined for each critical data element?
* Is there an integrated enterprise view, or a set of disconnected marts?
* Are repetitive and non repetitive data handled with different patterns?
* Is unstructured text linked back to structured master data keys?
* Is history kept in a way that supports audit and point in time analysis?
