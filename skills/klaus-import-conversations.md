---
name: Import conversations and users into Zendesk QA
description: Push help-desk conversations and their comments, plus the internal users who
  handle them, into Zendesk QA (formerly Klaus) from a custom or in-house help desk, then
  verify or remove what was imported.
api: openapi/klaus-public-import-api-openapi-original.json
operations:
  - PubImportApi_ImportUsers
  - PubImportApi_ImportTickets
  - PubImportApi_GetImportedTicket
  - PubImportApi_DeleteImportedTicket
generated: '2026-07-19'
method: generated
---

# Import conversations and users into Zendesk QA

Use this when a team reviews conversations that live in a help desk Zendesk QA has no native
connector for. Everything below is grounded in the published Public Import API specification.

## Before you start

- Create a custom integration in Zendesk QA (**Settings > Account > Connections > Add custom
  integration**) and copy its **API token** and **account ID**. Choose the retention period
  deliberately — conversations without manual reviews are deleted after it expires.
- Send the token as `Authorization: Bearer <api_token>` on every request.
- Base path is `/qa` on the account's own Zendesk subdomain; the documentation for
  non-migrated accounts publishes the shared host `https://pub.klausapp.com/v1` instead.

## Steps

1. **Import the internal users first** — `PubImportApi_ImportUsers`
   (`POST /api/import/users`). Email is the identity key. A conversation is only reviewable
   if its `assigneeEmail`, or a comment's `authorEmail`, matches an internal Zendesk QA user.
   Import users before conversations so nothing lands unreviewable.

2. **Import conversations in batches** — `PubImportApi_ImportTickets`
   (`POST /api/import/tickets`). Send a `data[]` array where each entry carries:
   - `id` — your own unique identifier. Keep it stable: it is the idempotency key, the
     lookup handle, and the join key every export uses.
   - `subject`, `description`, `assigneeName` — plain strings shown to reviewers.
   - `created` (and optionally `updated`) — `yyyy-mm-ddThh:mm:ss`.
   - `assigneeEmail` — must match an internal user.
   - `ticketFields[]` — optional `{id, label, values[{label, value}]}` entries that become
     filter facets in Zendesk QA.
   - `comments[]` — the replies. `comment` accepts Markdown or HTML. Set
     `authorIsNotClient: true` to add the `authorEmail` as an external user.

   Respect the published limits: **500 comments per ticket**, **10,000 tickets per import**,
   and **1,500 requests per minute per IP**.

3. **Verify a specific import** — `PubImportApi_GetImportedTicket`
   (`GET /api/import/tickets/{ticketExternalId}`) using the `id` you supplied. Use this as
   the post-batch spot check rather than re-importing to confirm.

4. **Correct or remove** — re-post the same `id` through `PubImportApi_ImportTickets` to
   update in place, or call `PubImportApi_DeleteImportedTicket`
   (`DELETE /api/import/tickets`) to remove one.

## Rules

- **Retries are safe.** Import is an upsert on your `id` — a repeated request updates rather
  than duplicates. There is no `Idempotency-Key` header; the id *is* the key.
- **Never invent a new id on retry.** A fresh id creates a second copy of the conversation
  and double-counts it in every export and dashboard.
- **Errors** come back as the grpc-gateway `google.rpc.Status` envelope
  (`code`, `message`, `details`) on a catch-all `default` response — the spec declares no
  per-status responses, so branch on the HTTP status and log `message` verbatim. A 401 means
  the bearer token is missing or wrong.
- **No rate-limit headers are documented.** Pace requests client-side against the 1,500/min
  ceiling rather than waiting for a `Retry-After`.
- Full conventions: `conventions/klaus-conventions.yml`. Errors: `errors/klaus-problem-types.yml`.
