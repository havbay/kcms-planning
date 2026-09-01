# Backend State

**Repository:** `/home/ggwp/dev/KCMS/KCMS-V2/kcms-backend`
**Remote:** `git@github.com:havbay/kcms-backend.git`
**Live:** https://kcms-backend.onrender.com

**Status:** Pilot onboarding, optional SMTP, Page Connection, and moderation
depth are deployed from `main` at commit `25f5dbc`. Render reports that deploy
live, health is `READY/REACHABLE`, the canonical OpenAPI contract matches local,
and anonymous OAuth start requests return `401`.

## Implemented

- Database-aware health, deterministic OpenAPI, email/password and optional
  Telegram identity, hashed bearer sessions, workspace isolation, team and
  settings.
- Public pilot requests, Platform Administrator decisions, seven-day one-time
  owner setup links, and provider-neutral SMTP with audited `MANUAL_REQUIRED`
  fallback.
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

The current local suite passes against PostgreSQL. Page Connection tests prove
session enforcement, approved-workspace gating, failed token validation,
workspace-scoped OAuth state, single use, encrypted storage, non-disclosure, and
disconnect. The approved-workspace guard was mutation-tested: deleting it made
the denial test return `201` instead of `403`; restoring it returned the suite to
green. Fernet round-trip and tamper rejection are also tested.

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
