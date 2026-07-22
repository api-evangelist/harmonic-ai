---
generated: '2026-07-22'
method: generated
name: Enrich a company
description: Resolve a company by domain/name/identifier with Harmonic, then fetch its full enriched profile (funding, headcount, team, traction) by id or URN.
api: openapi/harmonic-ai-openapi.json
operations: [get_company_by_identifiers_companies_post, get_company_by_id_or_urn_companies__id_or_urn__get]
source: >-
  Grounded in openapi/harmonic-ai-openapi.json (OpenAPI 3.1.0). Both
  operationIds verified verbatim in the spec; auth per
  authentication/harmonic-ai-authentication.yml, errors per
  errors/harmonic-ai-problem-types.yml, entity graph per
  data-model/harmonic-ai-data-model.yml.
---

# Enrich a company

Turn an identifier you already have (a website domain, company name, or LinkedIn URL) into a full Harmonic company profile.

## Auth
- Send your Harmonic API key in the `apikey` request header. See `authentication/harmonic-ai-authentication.yml` and `overlays/harmonic-ai-security-overlay.yaml`.
- Base URL: `https://api.harmonic.ai/`.

## Steps
1. **Resolve / enrich the company** — `get_company_by_identifiers_companies_post` (`POST /companies`). Pass the identifiers you have (e.g. `website_domain`, `name`, or a LinkedIn URL) in the request body. Harmonic resolves them to a canonical company and returns a `CompanyOutput`. Capture the returned `entity_urn` (`urn:harmonic:company:<id>`) or integer `id`.
2. **Fetch the full profile by id or URN** — `get_company_by_id_or_urn_companies__id_or_urn__get` (`GET /companies/{id_or_urn}`) with the `entity_urn` or `id` from step 1. This returns the complete enriched record: funding rounds, headcount, traction metrics, socials, team/people, and highlights.

## Notes
- `{id_or_urn}` accepts either the integer id or the full `urn:harmonic:company:<id>` — see `data-model/harmonic-ai-data-model.yml` for the URN grammar.
- Related entities hang off the company: `funding_rounds[]` (FundingRound → investors), `people[]`/employees, and `snapshots[]`. To pull the employee roster use `get_employees_companies__id_or_urn__employees_get`.

## Errors
- A missing/invalid identifier returns `422` with a FastAPI `{ "detail": [...] }` body — inspect `detail[].loc`/`detail[].msg`. See `errors/harmonic-ai-problem-types.yml`. This API is NOT RFC 9457.
