---
name: flexpa-find-payer-endpoint
description: Resolve a patient's stated insurer or provider to a connectable Flexpa network endpoint and the correct authentication flow, using the public directory API or the anonymous directory MCP server.
api: Flexpa
generated: '2026-08-14'
method: generated
source: https://www.flexpa.com/docs/network, https://www.flexpa.com/docs/network/directory-mcp, live probe of https://api.flexpa.com/endpoints and https://api.flexpa.com/mcp/directory
operations:
  - searchOrganization
---

# Find the right network endpoint

This is the one Flexpa surface that needs **no credentials at all** — both the
REST directory and the directory MCP server answer anonymously. Run it before
sending anyone into consent, so you know the plan is connectable.

## Two equivalent surfaces

- **REST:** `GET https://api.flexpa.com/endpoints` — query parameters `limit`
  (default 100, max 1000), `cursor`, `includeUnavailable`,
  `connectableViaConsent`. Cursor pagination: read `meta.hasMore` and
  `meta.nextCursor`, pass the cursor back to page. Verified 200 anonymously on
  2026-08-14.
- **MCP:** `https://api.flexpa.com/mcp/directory` (Streamable HTTP, no auth,
  60 requests/minute, 429 on exhaustion). Tools: `search_endpoints`,
  `get_endpoint_details`, `check_lob_support`, `list_endpoints`.
  Install: `claude mcp add flexpa-directory --transport http https://api.flexpa.com/mcp/directory`

## Steps

1. **Search by what the patient said.** Insurer name, brand, acronym or state.

2. **Handle the two traps the directory itself warns about.**
   - *Blue Cross Blue Shield is a federation, not one insurer.* If the patient
     says "Blue Cross" / "BCBS", ask which **state**, then search
     `BCBS <state>`. Anthem, Highmark and CareFirst are BCBS entities too.
   - *Medicaid is state-run and locally branded.* Ask which state, then search
     the programme name — Medi-Cal (CA), BadgerCare (WI), Peach State (GA).

3. **Confirm the line of business before you commit.** `check_lob_support` (or
   the organization `supports*LOB` flags on the REST record) takes one of
   `medicaid`, `medicare_advantage`, `chip`, `aca_on_exchange`,
   `aca_off_exchange`, `employer`, `original_medicare`, `veterans` and returns
   the authentication flow to use — OAuth (redirect to the payer) or Credentials
   (username/password captured in-app). If the patient says "individual" or
   "family", you must ask whether they bought through Healthcare.gov/a state
   exchange (`aca_on_exchange`) or straight from the insurer
   (`aca_off_exchange`).

4. **Check `status` before offering the connection.** Only `CONNECTED` endpoints
   are usable; the other values are `IN_PROGRESS`, `BROKEN`, `UNKNOWN`,
   `UNAVAILABLE`. Subscribe to the `endpoint_status_changed` webhook if you cache
   directory state.

5. **Check `resources[]`.** It lists the FHIR resource types that endpoint
   actually supports — do not promise a patient lab results from an endpoint that
   returns only Coverage and ExplanationOfBenefit.

## Notes

- The directory API is not described by any published OpenAPI; the fields above
  were read from the live response and the network reference documentation.
- `searchOrganization` and `searchPractitioner` on the FHIR API are a *different*
  thing: those search healthcare organizations and clinicians inside a consented
  patient's data, not the network of connectable payers.
