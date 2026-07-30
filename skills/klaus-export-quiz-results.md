---
name: Export Zendesk QA quiz results and leaderboards
description: Pull the quizzes an account has published, their per-question statistics,
  individual responses and the cross-quiz leaderboard out of Zendesk QA (formerly Klaus)
  for training and enablement reporting.
api: openapi/klaus-public-export-api-openapi-original.json
operations:
  - PublicExportApi_Quizzes
  - PublicExportApi_QuizOverview
  - PublicExportApi_QuizResponses
  - PublicExportApi_QuizResponse
  - PublicExportApi_QuizzesLeaderboard
generated: '2026-07-19'
method: generated
---

# Export Zendesk QA quiz results and leaderboards

Quizzes are the knowledge-check side of Zendesk QA. This flow lands quiz outcomes next to
review scores so enablement can see whether training moved quality.

## Before you start

API token and account ID from the Zendesk QA custom integration;
`Authorization: Bearer <api_token>`.

## Steps

1. **List the quizzes** — `PublicExportApi_Quizzes` (`GET /api/export/quizzes`). Each item
   carries `id`, `name`, `status` (`DRAFT`, `PUBLISHED`, `DELETED`, `ARCHIVED`), author,
   `publishedDate`, `numberOfParticipants`, `averageScorePercentage` and `workspaceIds`.
   Filter to `PUBLISHED` before reporting — drafts and archived quizzes carry stale stats.

2. **Get the question-level statistics** — `PublicExportApi_QuizOverview`
   (`GET /api/export/quizzes/{id}/overview`). Returns the quiz with `fields[]`, each field's
   `options[]` and per-option `selectedCount` / `percentageSelected` / `correct`. This is
   where you see *which* questions people get wrong.

3. **List the responses** — `PublicExportApi_QuizResponses`
   (`GET /api/export/quizzes/{id}/responses`). Each item carries `responseId`, `userId`,
   `userName` and `correctAnswerPercentage`.

4. **Drill into one response** — `PublicExportApi_QuizResponse`
   (`GET /api/export/quizzes/{id}/responses/{response}`) for a single submission with the
   per-field answers and `answeredCorrectly` flags.

5. **Pull the leaderboard** — `PublicExportApi_QuizzesLeaderboard`
   (`GET /api/export/quizzes/leaderboard`) for `quizzesTaken`, `ranking` and
   `averageScorePercentage` per user across all quizzes.

## Rules

- **Join to people by user id and name, not email** — the quiz payloads expose `userId`,
  `userName` and `userAvatar`, so resolve identities through `PublicExportApi_Users`
  (`GET /api/export/users`) if you need email.
- **Treat the leaderboard as derived, not authoritative history** — it is a current-state
  ranking; keep your own snapshots if you need a trend.
- **Page where paging is offered** (`page` / `pageSize`, `pagination.total`) and stay under
  1,500 requests per minute per IP.
- **Errors** use the `google.rpc.Status` envelope on a catch-all `default` response.
- Conventions: `conventions/klaus-conventions.yml`. Entity graph: `data-model/klaus-data-model.yml`.
