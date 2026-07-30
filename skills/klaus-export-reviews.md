---
name: Export Zendesk QA reviews for external analysis
description: Pull manual review data — scores, rating categories, reviewer and reviewee,
  comment threads and tags — out of Zendesk QA (formerly Klaus) account-wide or per
  workspace, with the scorecards and users needed to interpret it.
api: openapi/klaus-public-export-api-openapi-original.json
operations:
  - PublicExportApi_Workspaces
  - PublicExportApi_WorkspaceScorecards
  - PublicExportApi_WorkspaceUsers
  - PublicExportApi_Users
  - PublicExportApi_Reviews
  - PublicExportApi_AccountReviews
generated: '2026-07-19'
method: generated
---

# Export Zendesk QA reviews for external analysis

Use this to land review data in a warehouse or BI tool. Grounded in the published Public
Export API specification.

## Before you start

- Get the **API token**, **account ID** and **workspace ID** from the Zendesk QA custom
  integration (**Settings > Account > Connections**; the workspace ID is the number in the
  workspace URL).
- Send `Authorization: Bearer <api_token>`. Requests go to
  `https://<subdomain>.zendesk.com/qa/...`.
- Voice transcripts, AutoQA data and dashboard data are **not** part of the review export —
  AutoQA has its own endpoints (see the AutoQA and CSAT skill).

## Steps

1. **List the workspaces** — `PublicExportApi_Workspaces`
   (`GET /api/export/workspaces`) to resolve workspace ids and names.

2. **Pull the reference data you need to decode scores**:
   - `PublicExportApi_WorkspaceScorecards`
     (`GET /api/export/workspace/{workspace}/scorecards`) — scorecards with their rating
     categories, category groups and root causes.
   - `PublicExportApi_WorkspaceUsers`
     (`GET /api/export/workspace/{workspace}/users`) or `PublicExportApi_Users`
     (`GET /api/export/users`) for the account-wide roster.

3. **Export the reviews**:
   - Per workspace — `PublicExportApi_Reviews`
     (`GET /api/export/workspace/{workspace}/reviews`).
   - Account-wide — `PublicExportApi_AccountReviews` (`GET /api/export/reviews`).

   Window the pull with `fromDate` and `toDate` (`fromDate` is required on several export
   endpoints) and walk results with `page` / `pageSize`.

4. **Walk every page.** Each response carries a `pagination` object with `page`, `pageSize`
   and `total`. Keep requesting until you have consumed `total`.

## Reading the response

`conversations[]` each carry `externalId`, `url`, `externalUrl`, `lastUpdated` and
`reviews[]`. A review has `id`, `reviewer` and `reviewee` (email, name, avatar, groups),
`score`, `ratings[]` (`categoryId`, `categoryName`, `score`, `weight`, `critical`, `cause`),
`comment`, a nested `thread[]`, `tags[]`, `scorecard`, and created/updated timestamps.

`externalId` is the id supplied at import time — it is the join key back to your own help
desk and to every other Klaus export.

## Rules

- **Date-window, then page.** Re-running the same `fromDate`/`toDate` window is a safe,
  repeatable read; incremental loads should advance the window rather than rely on ordering.
- **Rate limit is 1,500 requests per minute per IP** and no rate-limit headers are
  documented — pace client-side.
- **Errors** arrive as `google.rpc.Status` (`code`, `message`, `details`) on the catch-all
  `default` response. Log `message` verbatim; do not assume a typed error body.
- Conventions: `conventions/klaus-conventions.yml`. Entity graph: `data-model/klaus-data-model.yml`.
