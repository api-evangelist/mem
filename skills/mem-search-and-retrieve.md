---
name: Search and retrieve notes with semantic + attachment context
description: Find the right Mem notes with relevance search, expand with attachment context and related notes, then read the full note.
api: openapi/mem-openapi-original.json
operations:
  - app_src_api_routes_external_v2_notes_views_search_notes
  - app_src_api_routes_external_v2_notes_views_extended_search_notes
  - app_src_api_routes_external_v2_notes_views_find_related_notes
  - app_src_api_routes_external_v2_notes_views_read_note
---

# Search and retrieve notes

Discover and read notes from the Mem Public Client API (`https://api.mem.ai`).

## Auth
`Authorization: Bearer $MEM_API_KEY` on every request.

## Steps

1. **Relevance search** — `POST /v2/notes/search` (`search_notes`) with a required free-text `query` plus optional structured filters (`filter_by_contains_*`, date-range filters). Returns a bounded, relevance-ranked snapshot with offset pagination (`limit`/`offset`).
2. **Search with attachment context** — when the answer may live in PDFs, images, audio, calendar events, or emails, use `POST /v2/notes/extended-search` (`extended_search_notes`). It returns note hits *plus* attachment match context and attachment IDs for follow-up.
3. **Expand from a known note** — `GET /v2/notes/{note_id}/related-notes` (`find_related_notes`) to surface semantically related notes; the source note is embedded at request time so freshly created/updated notes work before async indexing catches up.
4. **Read the full note** — `GET /v2/notes/{note_id}` (`read_note`) for canonical content, linked recording IDs, and attachment IDs. A trashed note still returns, with `trashed_at` set.

## Rules
- `filter_by_contains_*` flags combine with **OR** semantics.
- Use `search_notes` for lightweight discovery; use `extended_search_notes` only when you need attachment match detail.
- Errors use the `{ "error": { "type", ... } }` envelope; honor `429` + `Retry-After`.
