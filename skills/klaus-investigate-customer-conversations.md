---
name: Investigate a customer's conversations and their review history
description: Given an end customer's email address, find every Zendesk QA conversation for
  that customer, then pull the reviews, dispute history and calibration sessions attached
  to them.
api: openapi/klaus-public-export-api-openapi-original.json
operations:
  - PublicExportApi_ConversationSearch
  - PublicExportApi_Reviews
  - PublicExportApi_Disputes
  - PublicExportApi_CalibrationSessions
generated: '2026-07-19'
method: generated
---

# Investigate a customer's conversations and their review history

Use this for escalations and QA audits: start from the customer, end with the graded
conversations and any disputes raised against those grades.

## Before you start

Get the API token, account ID and workspace ID from the Zendesk QA custom integration and
send `Authorization: Bearer <api_token>`.

## Steps

1. **Find the conversations** — `PublicExportApi_ConversationSearch`
   (`POST /api/export/conversations/search`). This is a POST search, not a GET filter. Send:

   ```json
   { "endUserEmail": "user@example.com" }
   ```

   The response is `conversations[]` with `id` and `externalLink`. `externalLink` deep-links
   to the original help-desk conversation; the id is the `externalId` used everywhere else.
   Scorecard tags and comment hashtags are additional documented filters.

2. **Pull the reviews for the surrounding window** — `PublicExportApi_Reviews`
   (`GET /api/export/workspace/{workspace}/reviews`) with `fromDate`/`toDate` spanning the
   conversations you found, then match on `conversations[].externalId`. There is no
   per-conversation review endpoint, so filter client-side on the id set.

3. **Check for disputes** — `PublicExportApi_Disputes`
   (`GET /api/export/workspace/{workspace}/disputes`). Each dispute carries
   `conversationId`, `reviewId`, `type`, `status`, `createdBy`, `assignee`,
   `resolutionTime`, `categories[]` (with `expectedRating` and `rootCause`) and a comment
   thread — this is where a contested score and its reasoning live.

4. **Check calibration coverage** — `PublicExportApi_CalibrationSessions`
   (`GET /api/export/workspace/{workspace}/calibration-sessions`) to see whether any of the
   conversations were graded in a calibration session, where several reviewers score the
   same conversation independently.

## Rules

- **Search by email is exact.** If nothing comes back, confirm the address against the
  imported `comments[].authorEmail` rather than assuming the conversation is absent.
- **Do not widen the date window to brute-force a match.** Page the window you need
  (`page` / `pageSize`, `pagination.total`) and keep within 1,500 requests per minute per IP.
- **Read-only.** Nothing in this flow writes; the export API has no mutating operations.
- **Errors** use the `google.rpc.Status` envelope on a catch-all `default` response.
- Conventions: `conventions/klaus-conventions.yml`. Entity graph: `data-model/klaus-data-model.yml`.
