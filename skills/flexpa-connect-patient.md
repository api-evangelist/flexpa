---
name: flexpa-connect-patient
description: Connect a patient to their health plan through Flexpa Consent and obtain a Patient Access Token, then wait correctly for the payer sync to finish before reading data.
api: Flexpa
generated: '2026-08-14'
method: generated
source: openapi/flexpa-link-api-openapi.yml, openapi/flexpa-access-tokens-api-openapi.yml, https://www.flexpa.com/docs/consent
operations:
  - authorize
  - token
  - searchPatient
---

# Connect a patient with Flexpa Consent

Use this when an application needs a patient's own claims or clinical data. Flexpa
never lets your code see the payer credentials — the patient authorizes inside
Flexpa's hosted experience and you receive a token.

## Preconditions

- A publishable key (`pk_test_` or `pk_live_`) and a secret key (`sk_test_` / `sk_live_`).
- A `redirect_uri` pre-registered in Flexpa Portal.
- Test mode until an MSA is signed — live mode also requires your own public
  privacy policy and terms of service.

## Steps

1. **Generate PKCE material.** Create a code verifier and its S256 challenge.
   `code_challenge_methods_supported` is `["S256"]` and nothing else.

2. **Send the patient to `authorize`** (`GET /oauth/authorize`). Required query
   parameters: `response_type=code`, `client_id` (your publishable key),
   `redirect_uri`, `code_challenge`, `code_challenge_method=S256`, plus
   `scope` including `launch/patient` and `flexpa_external_id` — your own stable
   id for this user. Add `offline_access` to the scope only if you need refresh.
   Do **not** combine `flexpa_ial2_mode` with `flexpa_endpoint_id`; that
   combination has failed validation since 2026-07-10.

3. **Exchange the code** at `token` (`POST /oauth/token`,
   `application/x-www-form-urlencoded`) with `grant_type=authorization_code`,
   `code`, `code_verifier`, `redirect_uri`, `client_id`. The response carries
   `access_token`, `expires_in`, `scope`, and `refresh_token` when
   `offline_access` was granted.

4. **Wait for the sync, do not hammer it.** Immediately after consent, calls such
   as `searchPatient` return **HTTP 429 with an OperationOutcome whose
   `issue.code` is `transient`** — that means the payer sync is still running,
   typically under a minute, and the docs show `X-Retry-After: 3`. This is not a
   quota error. `issue.code: throttled` on a 429 is the real rate limit; back off
   using `Retry-After` and `X-RateLimit-Reset` only for that one.
   A `422` with `issue.code: processing` means the sync failed outright.

5. **Prefer the webhook to polling.** Subscribe to `sync_completed` (and
   `sync_failed`) in Portal and start reading when it arrives. Verify
   `X-Flexpa-Signature` (`t=…,v1=…`, HMAC-SHA256 over `{timestamp}.{raw_body}`,
   5-minute tolerance) and de-duplicate on `event_id`.

## Server-to-server variant

For application-level calls use `grant_type=client_credentials` at the same
`token` operation, authenticating with HTTP Basic (publishable key as username,
secret key as password). Those tokens are ES256 JWTs valid for 30 minutes.

## Notes

- There is no idempotency-key header on this API. Retries are safe on the GET
  operations; do not blindly replay a token exchange, an authorization code is
  single-use.
- Quote `X-Request-Id` from any failing response when contacting support.
