---
name: Export AutoQA ratings and CSAT from Zendesk QA
description: Pull automated quality scores (AutoQA reviews and their per-category ratings)
  and customer satisfaction results out of Zendesk QA (formerly Klaus), account-wide or per
  workspace, for coverage and correlation analysis against manual reviews.
api: openapi/klaus-public-export-api-openapi-original.json
operations:
  - PublicExportApi_AutoQAReviews
  - PublicExportApi_AutoQARatings
  - PublicExportApi_CSATByAccount
  - PublicExportApi_CSATByAccountAndWorkspace
generated: '2026-07-19'
method: generated
---

# Export AutoQA ratings and CSAT from Zendesk QA

AutoQA is the automated scoring pass over conversations; CSAT is the customer's own verdict.
Exporting both and joining them on the conversation id is how teams test whether their
quality scores predict satisfaction.

## Before you start

API token, account ID and workspace ID from the Zendesk QA custom integration;
`Authorization: Bearer <api_token>`.

## Steps

1. **Export AutoQA reviews** — `PublicExportApi_AutoQAReviews`
   (`GET /api/export/autoqa/reviews`). Rows carry `autoqaReviewId`, `accountId`,
   `workspaceId`, `workspaceName`, `connectionId`, `externalTicketId`,
   `conversationCreatedAt`, `isBot`, and the reviewee identity
   (`revieweeExternalId`, `revieweeInternalId`, `revieweeName`, `revieweeEmail`).

2. **Export the per-category AutoQA ratings** — `PublicExportApi_AutoQARatings`
   (`GET /api/export/autoqa/ratings`). Rows carry `autoqaRatingId` plus the
   `autoqaReviewId` they belong to. Join ratings to reviews on `autoqaReviewId`.
   Narrow with `autoqaCategoryType`, `ratingCategoryId`, `rootCauseCode`,
   `revieweeExternalId`, `connectionId`, `workspaceId` and `latestOnly` as needed.

3. **Page with the cursor.** The AutoQA exports return `nextCursor` alongside the
   `pagination` object — feed it back as the `cursor` query parameter until it is empty.
   This is the one place Klaus uses cursor paging rather than plain `page`/`pageSize`.

4. **Export CSAT** — `PublicExportApi_CSATByAccount` (`GET /api/export/csat`) for the whole
   account, or `PublicExportApi_CSATByAccountAndWorkspace`
   (`GET /api/export/workspace/{workspace}/csat`) per workspace. Rows carry
   `externalTicketId`, `conversationUrl`, `ticketCreatedAt`, `csatCreatedAt`,
   `csatAnsweredAt`, `agentName`/`agentEmail`, `customerName`/`customerEmail`, `csatScore`,
   `csatRawScore`, `csatComment`, `csatReason` and `conversationChannel`.

5. **Join.** `externalTicketId` on AutoQA rows and CSAT rows is the same
   `conversations[].externalId` the manual review export uses — it is the single spine
   across all three datasets.

## Rules

- **`latestOnly` changes the meaning of the pull.** Set it when you want the current state
  per conversation; leave it off when you want the full history of AutoQA passes.
- **`fromDate` is required on several export endpoints** — always send an explicit window
  rather than relying on a default.
- **Cursor state is not resumable across changed filters.** If you change a filter, restart
  the cursor.
- **Rate limit: 1,500 requests per minute per IP**, no documented rate-limit headers.
- **Errors** use the `google.rpc.Status` envelope on a catch-all `default` response.
- Conventions: `conventions/klaus-conventions.yml`. Entity graph: `data-model/klaus-data-model.yml`.
