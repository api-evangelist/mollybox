---
name: Capture a URL resource
description: Save a new URL into the Arcflow resource inbox and confirm it landed.
api: openapi/mollybox-arcflow-openapi.json
operations: [capture_resource_api_resources_post, list_resources_api_resources_get, get_resource_api_resources__resource_id__get]
---

# Capture a URL resource

Use this to save a link (from GitHub, X, WeChat, or the web) into Arcflow so it
can be triaged later.

## Auth
Every step here requires an `Authorization: Bearer <token>` header. The server is
single-user/self-hosted, so the token is a server-level access token.

## Steps
1. **Capture** — `POST /api/resources` (`capture_resource_api_resources_post`)
   with body `{ "url": "<the link>" }` (`CaptureRequest`). The response is the
   created `Resource`. Capturing the same canonical URL again increments
   `capture_count` rather than creating a duplicate — treat capture as
   upsert-by-URL.
2. **Confirm** — read `id`, `status` (defaults to `inbox`), `source_type`
   (auto-detected: github|x|wechat|web) and `metadata_status` (starts
   `pending`, becomes `fetched` once title/domain are enriched).
3. **Verify later** — `GET /api/resources/{resource_id}`
   (`get_resource_api_resources__resource_id__get`) to re-read, or
   `GET /api/resources?q=<keyword>` (`list_resources_api_resources_get`) to find
   it by keyword.

## Rules
- On `422` the body is `{"detail":[{loc,msg,type}...]}` — fix the offending
  field (usually a missing/invalid `url`) and retry.
- There is no Idempotency-Key; rely on canonical-URL de-duplication instead.
