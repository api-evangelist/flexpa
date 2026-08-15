---
name: flexpa-extract-tabular
description: Turn a consented patient's FHIR data into flat tabular rows with a SQL-on-FHIR ViewDefinition, or export the whole record as a PDF.
api: Flexpa
generated: '2026-08-14'
method: generated
source: openapi/flexpa-fhir-api-openapi.yml, openapi/flexpa-claims-data-api-openapi.yml, https://www.flexpa.com/docs/records
operations:
  - runViewDefinition
  - patientPdf
  - patientEverything
---

# Extract structured data

FHIR Bundles are awkward to analyse. Flexpa implements SQL-on-FHIR v2, so you can
declare the columns you want and get rows back instead of nested resources.

## Tabular extraction

`runViewDefinition` — `POST /fhir/ViewDefinition/$run`, request body
`application/fhir+json`, `Authorization: Bearer {access_token}`.

Send a `Parameters` resource whose `viewResource` parameter is a SQL-on-FHIR
`ViewDefinition`: it names the `resource` type, an optional `where`, and a
`select` of column expressions in FHIRPath. The response is the structured
extraction output.

Use it when you want, for example, one row per ExplanationOfBenefit line item
with the billed, allowed and patient-responsibility amounts — instead of walking
`item[]` in every Bundle entry yourself.

## Whole-record exports

- `patientEverything` — `GET /fhir/Patient/{id}/$everything`: every resource in
  the patient compartment as one Bundle. The raw ingest path.
- `patientPdf` — `GET /fhir/Patient/{id}/$pdf`: the comprehensive record as
  `application/pdf`. Since 2026-07-09 it renders WCAG AA colours, pivots vitals
  and labs horizontally, and de-duplicates conditions. This is the artifact to
  hand a human, not to parse.

## Notes

- Run these after `sync_completed`; before the sync finishes the FHIR surface
  answers 429 with `issue.code: transient`.
- Flexpa maintains a fork of the SQL-on-FHIR v2 implementation guide at
  https://github.com/flexpa/sql-on-fhir-v2 — use it for ViewDefinition syntax.
- The extraction runs inside the patient's authorization: you cannot query across
  patients with a single Patient Access Token.
