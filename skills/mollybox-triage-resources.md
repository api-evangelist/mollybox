---
name: Triage and open resources
description: Filter the resource list, advance items through the learning workflow, and open them.
api: openapi/mollybox-arcflow-openapi.json
operations: [list_resources_api_resources_get, update_resource_status_api_resources__resource_id__status_patch, open_resource_api_resources__resource_id__open_post]
---

# Triage and open resources

Move captured resources through the learning workflow
(`inbox -> next -> doing -> done -> archived`) and open them for reading.

## Auth
All steps require `Authorization: Bearer <token>`.

## Steps
1. **Find work** — `GET /api/resources`
   (`list_resources_api_resources_get`) filtered by `status=inbox` (repeatable),
   optional `q=<keyword>` and `source_type=github|x|wechat|web`. The response is
   an unbounded array of `Resource` (no pagination — filter narrowly).
2. **Advance status** — `PATCH /api/resources/{resource_id}/status`
   (`update_resource_status_api_resources__resource_id__status_patch`) with body
   `{ "status": "next" }` (`StatusUpdateRequest`). Valid values are the
   `ResourceStatus` enum: `inbox|next|doing|done|archived`.
3. **Open** — `POST /api/resources/{resource_id}/open`
   (`open_resource_api_resources__resource_id__open_post`) opens the resource's
   URL and records `last_opened_at`; the updated `Resource` is returned.

## Rules
- Sending a status outside the `ResourceStatus` enum returns `422`.
- An unknown `resource_id` returns `404` (`{"detail":"Not Found"}`).
