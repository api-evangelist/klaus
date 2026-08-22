# Klaus

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

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
