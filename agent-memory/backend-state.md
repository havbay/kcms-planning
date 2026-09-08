# Backend State

**Repository:** `/home/ggwp/dev/KCMS/KCMS-V2/kcms-backend`
**Remote:** `git@github.com:havbay/kcms-backend.git`
**Live:** https://kcms-backend.onrender.com

**Status:** Pilot onboarding, optional SMTP, multi-Page Connection, comment
moderation, and connected-Page Overview summaries are deployed from `main` at
commit `6f8ab19`. Render reports the deploy live; public health is
`READY/REACHABLE`, and OpenAPI exposes the Page collection and per-Page sync
routes.

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
- Workspace-scoped Facebook Page Connections. Facebook authorization and the
  advanced Page-token path converge on the same connection model. Page identity/tasks are
  provider-derived; credentials are Fernet-encrypted, never returned, and
  deleted on disconnect. OAuth state is hashed, scoped to user/workspace,
  expiring, and single-use.
- Automated Replies demo slice (local, not deployed): migration 021 adds
  workspace-level `auto_reply_enabled` setting,
  ordered workspace-owned rules, and an append-only decision-event log. The
  authenticated API supports owner-only settings, rule CRUD, reorder, safe
  simulation, and event reads. Page sync now processes newly imported safe
  comment matches through the Meta comment-reply edge when the owner enables
  replies; Messenger remains deferred.

## Layout

```text
migrations/            forward-only SQL 001-021
src/kcms/
├── api/               HTTP routes and transport schemas
├── auth/              identities, sessions and security
├── integrations/      Meta seam, encrypted credentials and Page repository
├── moderation/        classifier seam, matcher, repository and seeds
├── autoreply/          Khmer normalization, rule validation and reply pipeline
├── notifications/     SMTP contract and adapter
├── pilot/             public requests and owner setup
├── team/              membership and invitations
├── shared/database/   asyncpg pool and migrations
└── app.py             application factory
```

## Verification

The latest local run has 168 passing backend tests with the local PostgreSQL
compose service running. Automated Replies adds pure rule-engine coverage,
authenticated settings/rule/event coverage, and fake-provider tests for live,
safety, and idempotency. No Automated Replies code is deployed yet;
the real Meta-side post still needs a controlled deployment test.

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
  Facebook authorization, comment synchronization, and hide/unhide were exercised
  successfully against a controlled Page.

## Not yet implemented

Webhook/background ingestion, full moderation history, wider Platform
Administration, quality metrics with valid denominators, and rate limiting.
Messenger and broad automated-reply rollout remain deferred; the controlled
Facebook comment-reply demo is covered by ADR-0006.


## Facebook comment ingestion and action mirroring

`POST /api/v1/facebook/connections/{page_id}/sync` reads comments on that
Page's recent posts, classifies each new one through `PatternMatcher`, and
stores it in the workspace. The provider's own comment id is the primary key,
so re-syncing is idempotent: an imported comment keeps its verdict, actions and
corrections. The earlier workspace-wide `POST /api/v1/facebook/sync` is gone —
a workspace can hold several Pages, so a sync names the Page it is for.

`record_action` mirrors HIDE and UNHIDE to Facebook when the comment's
`page_id` matches one of the workspace's connected Pages, using that Page's own
credential. The Action row and the Graph
call share one transaction — if Meta refuses, the row rolls back and the caller
sees 502, because an Action records what actually happened to the comment.
Seeded sample comments carry the sandbox Page id, so they never send a hide for
an id Facebook does not know.

## Provider action result contract (2026-09-09)

Moderation action history now returns `provider_applied` alongside the action,
so clients can distinguish a real Facebook-side hide/unhide from a KCMS-only
sample action. The Graph adapter also rejects a successful HTTP response that
explicitly contains `success: false`; otherwise that response could be recorded
as a provider action without Facebook applying it.

`get_meta_client` requires only `META_GRAPH_VERSION`. Facebook Login checks its
own settings when used, so a Page token works without an OAuth app.

The Overview summary now applies the connected-Pages filter to both headline
totals and surfaced-reason counts; otherwise removed sample reasons could make
the chart disagree with its real comments. The filter uses `IN`, not a scalar
subquery, because a workspace may now connect several Pages. A regression test
covers that boundary. Locally, 44 tests pass and 69 database-dependent tests
skip because PostgreSQL is unavailable.

The Page Connections response accepts `TRIAL` as well as `STARTER` and `GROWTH`.
This matters for Clerk-created seven-day trial workspaces: omitting `TRIAL`
causes `GET /api/v1/facebook/connections` to raise a Pydantic 500 before any
Facebook operation begins. The regression is covered by the trial workspace
connection-status test in `tests/test_page_connections.py`.
