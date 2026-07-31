---
name: Capture a note and file it into a collection
description: Create a Mem note from content and add it to a (new or existing) collection, safely and idempotently.
api: openapi/mem-openapi-original.json
operations:
  - app_src_api_routes_external_v2_notes_views_create_note
  - app_src_api_routes_external_v2_collections_views_create_collection
  - app_src_api_routes_external_v2_collections_views_add_note_to_collection
---

# Capture a note and file it into a collection

Use the Mem Public Client API (`https://api.mem.ai`) to save a note and organize it under a collection.

## Auth
Send `Authorization: Bearer $MEM_API_KEY` on every request. Get the key from the API section in Mem settings.

## Steps

1. **Create the note** — `POST /v2/notes` (`create_note`). Put the note body in `content` as markdown; the **first line becomes the title**. Optionally supply your own `id` to make the create idempotent.
2. **Ensure the collection exists** — if you do not already have a collection id, `POST /v2/collections` (`create_collection`) with a `title`. You may supply your own `id`.
3. **Attach the note** — `PUT /v2/collections/{collection_id}/notes/{note_id}` (`add_note_to_collection`). This only creates the membership link; it does not modify note or collection content.

## Rules
- **Idempotency:** caller-provided `id` values on notes and collections are *create-only*. Re-sending a create with the same `id` returns **409 Conflict**, not an upsert — so a retried create will never duplicate the resource. Treat 409 as "already created" and proceed.
- **Errors:** responses use `{ "error": { "type", "message", "details" } }`; branch on `error.type`, not on `message`.
- **Rate limits:** honor `429` + `Retry-After`; watch `X-RateLimit-*` / `X-Complexity-*` headers. A `429` with `error.type: "quota_exceeded"` means a plan quota (not a rate limit) was hit — check `details.reset_time`.
