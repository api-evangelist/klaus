# Klaus

Klaus is an AI-powered conversation review and quality assurance (QA) platform for customer
support teams, founded in Tallinn, Estonia in 2019 and acquired by Zendesk in 2023, where it
now ships as **Zendesk QA**. The product samples support conversations from connected help
desks, scores them against configurable scorecards and rating categories, runs AutoQA checks,
tracks disputes and calibration sessions, measures CSAT, and delivers coaching, quizzes and
agent-performance dashboards.

- Website: https://www.klausapp.com/ (redirects to https://www.zendesk.com/service/quality-assurance/)
- API reference: https://pub.klausapp.com/
- Product guide: https://support.zendesk.com/hc/en-us/sections/6999625340058-Using-Zendesk-QA

## APIs

| API | What it does | Spec |
|---|---|---|
| Klaus Public Export API | Export reviews, AutoQA ratings and reviews, CSAT, disputes, calibration sessions, workspace scorecards, quizzes and users | `openapi/klaus-public-export-api-openapi-original.json` (Swagger 2.0, 18 operations) |
| Klaus Public Import API | Bulk-import conversations with comments and ticket fields, fetch or delete an imported ticket, bulk-import users | `openapi/klaus-public-import-api-openapi-original.json` (Swagger 2.0, 4 operations) |

Both are authenticated with a bearer API token issued from a Zendesk QA custom integration
(`Authorization: Bearer <api_token>`) — no OAuth, and therefore no scopes.

## Artifacts

- `openapi/` — both published Swagger 2.0 specifications, harvested verbatim from pub.klausapp.com
- `overlays/` — API Evangelist Overlay 1.0.0 enhancements over each spec
- `authentication/` — the auth profile and where each credential comes from
- `conventions/` — pagination, idempotency, error envelope, versioning, filtering
- `errors/` — the grpc-gateway `google.rpc.Status` envelope and the documented gaps
- `rate-limits/` — 1,500 req/min per IP; 500 comments/ticket; 10,000 tickets/import
- `lifecycle/` — versioning, deprecation announcements, data retention, status page
- `changelog/` — recent dated Zendesk QA announcements
- `conformance/` — standards conformance plus the Zendesk compliance program
- `data-model/` — the entity-relationship graph derived from the specs
- `mcp/` — a candidate MCP tool surface derived from all 22 operations (no official server exists)
- `llms/` — a generated `llms.txt` (none is published upstream)
- `security/` — domain-security probe, vulnerability disclosure, trust center
- `skills/` — five packaged Agent Skills for the marquee flows
- `well-known/` — the probed `/.well-known/` surface (nothing published)
