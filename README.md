# Flexpa (flexpa)

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Flexpa is a patient-access platform that lets applications connect a patient to their health insurance plan and retrieve claims and clinical data as normalized FHIR R4 resources. Patients authorize access through Flexpa Link / OAuth 2.0 PKCE, and applications read ExplanationOfBenefit, Coverage, Patient, and other resources from a single FHIR API at https://api.flexpa.com.

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/flexpa/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/flexpa/refs/heads/main/apis.yml)

## Tags

- Healthcare
- FHIR
- Patient Access
- Claims Data
- Health Insurance

## Timestamps

- **Created:** 2026-06-21
- **Modified:** 2026-06-21

## APIs

### Flexpa Link / Connect API

Patient consent and connection flow. Flexpa Link walks a patient through selecting their health plan and authorizing access; the OAuth 2.0 PKCE authorization endpoint (GET /oauth/authorize) returns an authorization code that is exchanged for a Patient Access Token.

- **Human URL:** [https://www.flexpa.com/docs/tools/link](https://www.flexpa.com/docs/tools/link)
- **Base URL:** `https://api.flexpa.com`

#### Tags

- Link
- Connect
- OAuth
- PKCE

#### Properties

- [Documentation](https://www.flexpa.com/docs/tools/link)
- [API Reference](https://www.flexpa.com/docs/consent)
- [OpenAPI](openapi/flexpa-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/flexpa.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/flexpa.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Flexpa Access Tokens API

Token endpoint (POST /oauth/token) that exchanges an authorization code plus PKCE verifier for a Patient Access Token, refreshes tokens via the refresh_token grant, and mints server-to-server Application Access Tokens via the client_credentials grant using publishable and secret keys.

- **Human URL:** [https://www.flexpa.com/docs/consent](https://www.flexpa.com/docs/consent)
- **Base URL:** `https://api.flexpa.com`

#### Tags

- Access Tokens
- OAuth
- Authentication

#### Properties

- [Documentation](https://www.flexpa.com/docs/consent)
- [Documentation](https://www.flexpa.com/blog/introducing-application-access-tokens-server-to-server-authentication-for-the-flexpa-fhir-api)
- [OpenAPI](openapi/flexpa-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/flexpa.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/flexpa.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Flexpa FHIR Resources API

FHIR R4 read and search across patient compartment resources including Patient, Coverage, ExplanationOfBenefit, Condition, Observation, MedicationRequest, Practitioner, and Organization, plus the capability statement at GET /fhir/metadata.

- **Human URL:** [https://www.flexpa.com/docs/records](https://www.flexpa.com/docs/records)
- **Base URL:** `https://api.flexpa.com/fhir`

#### Tags

- FHIR
- ExplanationOfBenefit
- Coverage
- Patient

#### Properties

- [Documentation](https://www.flexpa.com/docs/records)
- [Documentation](https://www.flexpa.com/docs/fhir/explanation-of-benefit)
- [OpenAPI](openapi/flexpa-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/flexpa.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/flexpa.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Flexpa Claims Data API

Health-insurance claims delivered as FHIR ExplanationOfBenefit resources, plus patient-portability operations such as Patient $everything, the International Patient Summary ($summary), and PDF export ($pdf).

- **Human URL:** [https://www.flexpa.com/docs/fhir/explanation-of-benefit](https://www.flexpa.com/docs/fhir/explanation-of-benefit)
- **Base URL:** `https://api.flexpa.com/fhir`

#### Tags

- Claims Data
- ExplanationOfBenefit
- Portability
- Patient Summary

#### Properties

- [Documentation](https://www.flexpa.com/docs/fhir/explanation-of-benefit)
- [Documentation](https://www.flexpa.com/docs/records)
- [OpenAPI](openapi/flexpa-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/flexpa.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/flexpa.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

## Common Properties

- [GitHub Organization](https://github.com/flexpa)
- [LinkedIn](https://www.linkedin.com/company/flexpa)
- [Website](https://www.flexpa.com)
- [Documentation](https://www.flexpa.com/docs)
- [Plans](plans/flexpa-plans-pricing.yml)
- [Rate Limits](rate-limits/flexpa-rate-limits.yml)
- [Fin Ops](finops/flexpa-finops.yml)

## Maintainers

**FN:** Kin Lane
**Email:** kin@apievangelist.com
