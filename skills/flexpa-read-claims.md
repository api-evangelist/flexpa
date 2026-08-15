---
name: flexpa-read-claims
description: Read a consented patient's insurance coverage and adjudicated claims from the Flexpa FHIR API, with correct pagination and 429 handling.
api: Flexpa
generated: '2026-08-14'
method: generated
source: openapi/flexpa-claims-data-api-openapi.yml, openapi/flexpa-fhir-api-openapi.yml, https://www.flexpa.com/docs/records
operations:
  - searchCoverage
  - readCoverage
  - searchExplanationOfBenefit
  - readExplanationOfBenefit
  - patientEverything
  - patientSummary
---

# Read coverage and claims

Runs after `flexpa-connect-patient`. Everything here is FHIR R4 against
`https://api.flexpa.com/fhir` with `Authorization: Bearer {access_token}`.

## Steps

1. **Establish coverage first.** `searchCoverage` (`GET /fhir/Coverage`) accepts
   `patient` and `status`. Each Coverage names the payer and the member id, and
   is what the claims below hang off. `readCoverage`
   (`GET /fhir/Coverage/{id}`) fetches one.

2. **Pull the claims.** `searchExplanationOfBenefit`
   (`GET /fhir/ExplanationOfBenefit`) is the financial core. Documented search
   parameters: `patient`, `created`, `provider`, `coverage`, `status`,
   `identifier`. `readExplanationOfBenefit` (`GET /fhir/ExplanationOfBenefit/{id}`)
   fetches a single adjudicated claim.

3. **Paginate the FHIR way.** `_count` sets page size (default 20, maximum 1000)
   and `_offset` the offset; follow `Bundle.link` relations `next` / `previous`
   rather than constructing offsets yourself, and read `Bundle.total`.

4. **Or take the whole compartment.** `patientEverything`
   (`GET /fhir/Patient/{id}/$everything`) returns every resource in the patient
   compartment in one Bundle — the right call for a first full ingest.
   `patientSummary` (`GET /fhir/Patient/{id}/$summary`) returns an International
   Patient Summary document instead, which is the better choice for a human- or
   model-readable snapshot.

## Failure handling

| Status | `issue.code` | Meaning | What to do |
|---|---|---|---|
| 429 | `transient` | Payer sync still running | Retry after ~3s; not a quota problem |
| 429 | `throttled` | Rate limited | Honour `Retry-After`, `X-RateLimit-Reset` |
| 422 | `processing` | Bad request, or the payer sync failed | Do not retry blindly |
| 403 | `forbidden` | Token lacks patient context/scope | Re-consent with `launch/patient` |
| 404 | `not-supported` | Operation unsupported for that endpoint | Check the CapabilityStatement |

Errors are FHIR `OperationOutcome` in `application/fhir+json` — **not** RFC 9457
problem+json. Parse `issue[].code` and `issue[].diagnostics`.

## Notes

- Not every connected endpoint supports every resource type. Call
  `getCapabilityStatement` (`GET /fhir/metadata`), or read the `resources[]` array
  on the endpoint record in the network directory, before assuming a resource type
  is available.
- Financial interpretation guidance is at
  https://www.flexpa.com/docs/guides/financial.
