---
name: Capture anything with Mem It (asynchronous intelligent save)
description: Send raw content to Mem It and let Mem intelligently file it; handle the higher complexity cost and async processing.
api: openapi/mem-openapi-original.json
operations:
  - app_src_api_routes_external_v2_mem_it_views_mem_it
---

# Capture anything with Mem It

"Mem It" is Mem's intelligent capture endpoint: send content and Mem decides how to store and organize it.

## Auth
`Authorization: Bearer $MEM_API_KEY`.

## Steps

1. **Submit content** — `POST /v2/mem-it` (`mem_it`) with the content to remember. As of the `/v2` series, Mem It is **asynchronous by default** with robust error handling.
2. **Expect deferred processing** — the note is created and organized asynchronously; do not assume it is immediately searchable. Use the note/search skills to confirm once indexing completes.

## Rules
- **Complexity cost:** `/v2/mem-it` costs **40 complexity tokens** per call (most endpoints cost 1). With a 200/minute complexity budget that is ~5 Mem It calls per minute. Watch `X-Complexity-*` headers and back off on `429`.
- **Quota vs rate limit:** a `429` with `error.type: "quota_exceeded"` is a plan quota (see `details.reset_time`), not a transient rate limit.
- **Roadmap:** request status tracking for Mem It operations and "apply a template" support are planned per the changelog — do not depend on them yet.
