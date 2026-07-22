---
generated: '2026-07-22'
name: Build and populate a company watchlist
method: generated
description: Create a Harmonic company watchlist (the MCP "list"), add companies to it by URN, and read back the entries.
api: openapi/harmonic-ai-openapi.json
operations: [create_company_watchlist_watchlists_companies_post, add_companies_to_company_watchlist_watchlists_companies__id_or_urn__addCompanies_post, get_company_watchlist_entries_watchlists_companies__id_or_urn__entries_get]
source: >-
  Grounded in openapi/harmonic-ai-openapi.json. All three operationIds verified
  verbatim in the spec; entity graph per data-model/harmonic-ai-data-model.yml;
  MCP naming ("list" == watchlist) per mcp/harmonic-ai-tool-crosswalk.yml.
---

# Build and populate a company watchlist

Create a company watchlist, fill it with companies you found via search/enrichment, and confirm its contents. The REST "watchlist" is what the MCP surface calls a "list".

## Auth
- Send your Harmonic API key in the `apikey` request header. See `authentication/harmonic-ai-authentication.yml`.
- Base URL: `https://api.harmonic.ai/`.

## Steps
1. **Create the watchlist** — `create_company_watchlist_watchlists_companies_post` (`POST /watchlists/companies`). Pass a `name` (and optionally `shared_with_team`) in the request body (`CompanyWatchlistInput`). Capture the returned `id` / `entity_urn` (`urn:harmonic:company_watchlist:<id>`).
2. **Add companies** — `add_companies_to_company_watchlist_watchlists_companies__id_or_urn__addCompanies_post` (`POST /watchlists/companies/{id_or_urn}:addCompanies`) with the watchlist `id_or_urn` from step 1 and a body listing the company URNs (`urn:harmonic:company:<id>`) to add.
3. **Read the entries** — `get_company_watchlist_entries_watchlists_companies__id_or_urn__entries_get` (`GET /watchlists/companies/{id_or_urn}/entries`). Returns the `CompanyWatchlistEntry` list (each with `company_urn`, `entry_urn`, and any `custom_field_values`).

## Notes
- Alternative add path: `company_watchlist_add_entries_watchlists_companies__id_or_urn__entries_post` (`POST /watchlists/companies/{id_or_urn}/entries`) adds entries directly.
- To remove: `remove_companies_from_company_watchlist_watchlists_companies__id_or_urn__removeCompanies_post`. To define custom columns on the list: `create_company_watchlist_custom_field_watchlists_companies__id_or_urn__custom_field_post`.
- The parallel people flow uses `create_people_watchlist_watchlists_people_post` / `add_people_to_people_watchlist_watchlists_people__id_or_urn__addPeople_post` / `get_people_watchlist_entries_watchlists_people__id_or_urn__entries_get`.

## Errors
- Invalid watchlist id or company URN returns `422` FastAPI validation errors (`{ "detail": [...] }`); see `errors/harmonic-ai-problem-types.yml`.
