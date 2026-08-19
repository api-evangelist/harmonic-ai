---
generated: '2026-08-14'
method: generated
name: Attach a document to an investor record
description: Upload a diligence document to a Harmonic investor record via the three-step presigned-upload flow, link it, read it back, and clean it up.
api: openapi/harmonic-ai-investors-api-openapi.yml
operations: [batch_create_attachments_investors__id_or_urn__attachments_post, batch_complete_upload_investors__id_or_urn__attachments_put, link_attachments_investors__id_or_urn__attachments_patch, batch_get_attachments_investors_attachments_post, get_attachment_content_investors__id_or_urn__attachments__urn__get, delete_attachment_investors__id_or_urn__attachments__urn__delete, batch_delete_attachments_investors_attachments_delete]
source: >-
  Grounded in openapi/harmonic-ai-investors-api-openapi.yml, split from the live spec captured
  2026-08-14 at https://api.harmonic.ai/openapi.json (source in openapi/_original/). Every
  operationId verified verbatim in that spec. Auth per authentication/harmonic-ai-authentication.yml,
  errors per errors/harmonic-ai-problem-types.yml, retry/limit semantics per
  conventions/harmonic-ai-conventions.yml and rate-limits/harmonic-ai-rate-limits.yml.
---

# Attach a document to an investor record

Harmonic's `investors` attachment surface is new — it appeared between the 2026-07-22 and 2026-08-14 captures of the provider's own OpenAPI (see `changelog/harmonic-ai-changelog.yml`). Use it to hang diligence memos, decks and notes off an investor entity.

## Auth
- Send your Harmonic API key in the `apikey` request header. See `authentication/harmonic-ai-authentication.yml`.
- Base URL: `https://api.harmonic.ai/`.

## Steps

1. **Create the attachment slot(s)** — `batch_create_attachments_investors__id_or_urn__attachments_post` (`POST /investors/{id_or_urn}/attachments`). `{id_or_urn}` accepts the integer id or the `urn:harmonic:investor:<id>` URN. This registers the attachment metadata and returns the upload target(s).
2. **Upload the bytes**, then **mark the upload complete** — `batch_complete_upload_investors__id_or_urn__attachments_put` (`PUT /investors/{id_or_urn}/attachments`). An attachment is not readable until this call succeeds.
3. **Link the attachment** — `link_attachments_investors__id_or_urn__attachments_patch` (`PATCH /investors/{id_or_urn}/attachments`) to associate the completed attachment with the investor record.
4. **Read it back** — `get_attachment_content_investors__id_or_urn__attachments__urn__get` (`GET /investors/{id_or_urn}/attachments/{urn}`) for a single attachment's content, or `batch_get_attachments_investors_attachments_post` (`POST /investors/attachments`) to fetch metadata for many attachment URNs in one call.
5. **Clean up** — `delete_attachment_investors__id_or_urn__attachments__urn__delete` (`DELETE /investors/{id_or_urn}/attachments/{urn}`) for one, or `batch_delete_attachments_investors_attachments_delete` (`DELETE /investors/attachments`) for many.

## Rules an agent must follow

- **There is no idempotency key.** Harmonic documents none, and none exists anywhere in the 93-operation spec (`conventions/harmonic-ai-conventions.yml`). Step 1 is a non-idempotent `POST`: if it times out, do **not** blind-retry. Re-read with `batch_get_attachments_investors_attachments_post` first and only create what is genuinely missing, or you will leave orphaned attachment slots.
- **The flow is stateful and ordered.** Create → complete → link. Skipping step 2 leaves an attachment that steps 3–4 cannot resolve.
- **Batch ceilings.** Batch operations across this API cap at 50 IDs/URNs per request (`conventions/harmonic-ai-conventions.yml`). Chunk larger sets.
- **Rate limit.** 10 requests/second per key; on `429` read `X-RateLimit-Remaining-Second` and back off for the remainder of the second. Note that a `429` elsewhere in this API can instead mean monthly enrichment-credit exhaustion, which is **not** retryable — check which endpoint returned it. See `rate-limits/harmonic-ai-rate-limits.yml`.
- **Errors are not RFC 9457.** Every failure is `application/json` with a FastAPI `detail` array, not `application/problem+json`. Parse `detail[].loc` / `detail[].msg`. See `errors/harmonic-ai-problem-types.yml`.
- **Capture `x-request-id`** from every response — it is CORS-exposed on all responses including errors and is the identifier Harmonic support needs. It is undocumented; this repo recorded it from live observation.
