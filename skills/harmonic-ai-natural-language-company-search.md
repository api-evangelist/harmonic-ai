---
generated: '2026-07-22'
name: Natural-language company search
method: generated
description: Find companies on Harmonic from a natural-language / keyword description, with as-you-type typeahead to disambiguate a specific company.
api: openapi/harmonic-ai-openapi.json
operations: [search_companies_by_keywords_search_companies_by_keywords_post, typeahead_search_search_typeahead_get, search_agent_search_search_agent_get]
source: >-
  Grounded in openapi/harmonic-ai-openapi.json. All three operationIds verified
  verbatim in the spec; crosswalk to MCP tools in
  mcp/harmonic-ai-tool-crosswalk.yml.
---

# Natural-language company search

Go from a description ("seed-stage AI infra startups in Europe") or a partial name to matching Harmonic companies.

## Auth
- Send your Harmonic API key in the `apikey` request header. See `authentication/harmonic-ai-authentication.yml`.
- Base URL: `https://api.harmonic.ai/`.

## Steps
1. **Keyword / NL search** — `search_companies_by_keywords_search_companies_by_keywords_post` (`POST /search/companies_by_keywords`). Send your keyword/natural-language query in the request body. Returns a paged list of matching company URNs/records.
2. **(Optional) Agent search** — `search_agent_search_search_agent_get` (`GET /search/search_agent`) is the agent-oriented search entry point for a single free-text query when you want Harmonic to route the query server-side.
3. **(Optional) Typeahead disambiguation** — `typeahead_search_search_typeahead_get` (`GET /search/typeahead`) for as-you-type matching when the user is homing in on one specific company/entity by name.
4. **Enrich the hits** — take any `urn:harmonic:company:<id>` from the results and call `get_company_by_id_or_urn_companies__id_or_urn__get` (`GET /companies/{id_or_urn}`) for the full profile (see the `enrich-a-company` skill).

## Notes
- The MCP `search_companies_natural_language` tool maps to `search_companies_by_keywords` and/or `search_agent`; `typeahead_search` maps 1:1. See `mcp/harmonic-ai-tool-crosswalk.yml`.
- To find look-alikes of a known company, use `search_similar_companies_search_similar_companies__id_or_urn__get` (`GET /search/similar_companies/{id_or_urn}`).

## Errors
- Malformed query bodies return `422` FastAPI validation errors (`{ "detail": [...] }`); see `errors/harmonic-ai-problem-types.yml`.
