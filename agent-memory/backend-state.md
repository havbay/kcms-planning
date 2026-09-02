# Backend State

**Repository:** `/home/ggwp/dev/KCMS/KCMS-V2/kcms-backend`
**Remote:** `git@github.com:havbay/kcms-backend.git`
**Live:** https://kcms-backend.onrender.com

**Status:** Pilot onboarding, optional SMTP, Page Connection, and moderation
depth are deployed from `main` at commit `4b5addb`. Render reports that deploy
live, health is `READY/REACHABLE`, the OpenAPI contract matches local
semantically, public signup is absent, and anonymous OAuth starts return `401`.

## Implemented

- Database-aware health, deterministic OpenAPI, email/password and optional
  Telegram identity, hashed bearer sessions, workspace isolation, team and
  settings.
- Public pilot requests, Platform Administrator decisions, seven-day one-time
  owner setup links, and provider-neutral SMTP with audited `MANUAL_REQUIRED`
  fallback.
- Public email signup is disabled and hidden from OpenAPI. After reviewed
  onboarding, any authenticated Client workspace may start Facebook
  authorization; sample-data status adds no second approval boundary.
- PatternMatcher behind the `Classifier` protocol with severity and target,
  independent confidence, abstention, version, and surfaced reason.
- Moderation list with server-side query, severity, target, surfaced-reason,
  review-status, sort, offset/limit, stable tie-breaker, source post/caption/type,
  parent context, actions, Corrections, and database-computed summary.
- One workspace-scoped Facebook Page Connection. Facebook authorization and the
  advanced Page-token path converge on the same record. Page identity/tasks are
  provider-derived; credentials are Fernet-encrypted, never returned, and
  deleted on disconnect. OAuth state is hashed, scoped to user/workspace,
  expiring, and single-use.

## Layout

```text
migrations/            forward-only SQL 001-011
src/kcms/
├── api/               HTTP routes and transport schemas
├── auth/              identities, sessions and security
├── integrations/      Meta seam, encrypted credentials and Page repository
├── moderation/        classifier seam, matcher, repository and seeds
├── notifications/     SMTP contract and adapter
├── pilot/             public requests and owner setup
├── team/              membership and invitations
├── shared/database/   asyncpg pool and migrations
└── app.py             application factory
```

## Verification

All 77 current tests pass against PostgreSQL. Page Connection tests prove
session enforcement, direct Client OAuth start, failed token validation,
workspace-scoped OAuth state, single use, encrypted storage, non-disclosure, and
disconnect. Contract tests prove the removed Page-approval API cannot return to
OpenAPI. Fernet round-trip and tamper rejection are also tested.

## Environment

In addition to database, CORS, admin, Telegram, and optional SMTP configuration,
Page Connection uses `META_GRAPH_VERSION`, `META_APP_ID`, `META_APP_SECRET`,
`META_LOGIN_CONFIG_ID`, `META_OAUTH_REDIRECT_URI`, `META_OAUTH_SCOPES`, and
`INTEGRATION_ENCRYPTION_KEY`. Missing Meta or encryption configuration fails the
integration closed with `503`. Facebook authorization URLs include the
configured Facebook Login for Business `config_id`; the default permission set
includes Page discovery, engagement reads, user-content reads, engagement
management, and Page webhook metadata management.

## Operational notes

- Render's GitHub webhook previously did not fire; verify a deploy's `finishedAt`
  rather than assuming a push is live.
- cron-job.org pings `/api/v1/health` for the free instance.
- The Meta application and Render variables have been configured by the owner.
  The configuration-aware release is live with no Render error logs, but a
  successful authorization response has not yet been observed by KCMS.

## Not yet implemented

Comment synchronization, webhook ingestion, provider-side hide/unhide, full
moderation history, wider Platform Administration, quality metrics with valid
denominators, and rate limiting.


## Facebook comment ingestion and action mirroring

`POST /api/v1/facebook/sync` reads comments on the connected Page's recent
posts, classifies each new one through `PatternMatcher`, and stores it in the
workspace. The provider's own comment id is the primary key, so re-syncing is
idempotent: an imported comment keeps its verdict, actions and corrections.

`record_action` mirrors HIDE and UNHIDE to Facebook when the comment's
`page_id` matches the workspace's connected Page. The Action row and the Graph
call share one transaction — if Meta refuses, the row rolls back and the caller
sees 502, because an Action records what actually happened to the comment.
Seeded sample comments carry the sandbox Page id, so they never send a hide for
an id Facebook does not know.

`get_meta_client` requires only `META_GRAPH_VERSION`. Facebook Login checks its
own settings when used, so a Page token works without an OAuth app.

The Overview summary now applies the connected-Page filter to both headline
totals and surfaced-reason counts; otherwise removed sample reasons could make
the chart disagree with its four real comments. A regression test covers that
boundary. Locally, 44 tests pass and 68 database-dependent tests skip because
PostgreSQL is unavailable.
