---
name: Send analytics events to Helika
description: >-
  Ingest gameplay or community engagement events into the Helika Analytics
  Service using the batch events endpoint with x-api-key authentication.
api: openapi/helika-events-openapi.json
operations:
- create_events_events__post
---

# Send analytics events to Helika

Use this skill to record gameplay/engagement events in the Helika Analytics
Service.

## Prerequisites
- A Helika API key issued from the platform (https://platform.helika.io/). The
  SDKs distinguish DEV and PROD keys — use the key that matches your target
  environment.
- Base URL (production): `https://events.analytics.helika.io`

## Authentication
Send the API key on every request as the `x-api-key` header (see
`authentication/helika-authentication.yml`). No OAuth or scopes are involved.

## Steps
1. Build an `EventRequest` body containing an `events` array. Each event
   (`Event`) requires:
   - `game_id` — unique identifier for the game
   - `event_type` — e.g. `game_start`, `game_end`
   - `event` — a JSON object with the event payload
   - `created_at` (optional) — ISO 8601 timestamp
   Optionally set the top-level `id` to a unique request identifier so you can
   deduplicate on the client (`conventions/helika-conventions.yml`).
2. Call `create_events_events__post` — `POST /events/` — with the `x-api-key`
   header and the JSON body.
3. On `200`, read the `Response` envelope: `status` is one of `ok`, `error`,
   or `accepted`, with a human `message`.
4. On `422`, inspect the `HTTPValidationError` `detail[]` array
   (`errors/helika-problem-types.yml`) — each entry's `loc` points at the
   failing field. Fix the body and retry.

## Notes
- Batch multiple events in a single call via the `events[]` array rather than
  issuing one request per event.
- Prefer the first-party SDKs (`packages/helika-packages.yml`) — Web
  (`helika-sdk`), Unity, and Unreal — which wrap this endpoint and the
  DEV/PROD environment switch.
