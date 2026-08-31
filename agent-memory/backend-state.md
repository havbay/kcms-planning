# Backend State

**Repository:** `/home/ggwp/dev/KCMS/KCMS-V2/kcms-backend`
**Remote:** `git@github.com:havbay/kcms-backend.git`
**Live:** https://kcms-backend.onrender.com

**Status:** The previously approved stack is deployed on Render. Pilot onboarding
and optional SMTP delivery are implemented and verified locally on
`feature/moderation-slice`; they are not live until reviewed, pushed and deployed.

## Implemented

**Health** — `GET /api/v1/health` runs `SELECT 1` through the asyncpg pool.
Returns `200 READY/REACHABLE` or `503 DEGRADED/UNREACHABLE`, never exception or
connection detail. A failed connection does not crash startup; the reason is
logged so a misconfigured `DATABASE_URL` is distinguishable from a missing one.

**Classification** — `moderation/pattern_matcher.py` implements the `Classifier`
Protocol. Two axes with independent confidences, abstention, and a
`surfaced_reason` on every verdict. Model version `pattern-matching-v0.1`.

**Moderation** — paginated work list, actions (`HIDE`/`LEAVE`/`UNHIDE`) and
corrections. A workspace-wide summary is computed in the database rather than
from a page of results.

**Identity** — email with scrypt, Telegram Login Widget with HMAC verification.
Sessions are bearer tokens stored only as a SHA-256 hash. Identity is per
provider, so one account can hold both.

**Workspaces** — every account owns an isolated workspace seeded with its own
copy of the sample comments. Cross-workspace access returns 404, never 403.

**Access requests** — a sandbox workspace requests a Page connection; a Platform
Administrator approves or declines. Approval lifts `workspace.is_sandbox`.

**Pilot onboarding** — a visitor submits a public request without creating an
account. A Platform Administrator approves or declines it. Approval creates an
approved workspace and a seven-day, single-use owner setup link; the invitee
chooses their own password. Existing accounts are upgraded instead of duplicated.

**Notifications** — provider-neutral email contract with an SMTP adapter. Every
attempt is audited. When SMTP is incomplete or unavailable, approval remains
usable through an administrative copy-link fallback marked `MANUAL_REQUIRED`.

**Team** — membership with `owner`/`member`, and single-use invitation links
that expire in seven days. Only the token hash is stored.

**Settings** — workspace rename (owner only) and personal display name (anyone).

## Layout

```
migrations/            forward-only SQL, applied at startup, numbered 001-008
src/kcms/
├── api/               routing and transport schemas
├── auth/              identity, sessions, security primitives
├── access/            page connection requests
├── pilot/             public requests and owner setup invitations
├── notifications/     delivery contract and SMTP adapter
├── team/              membership and invitations
├── moderation/        classifier seam, pattern matcher, repository, seeds
├── shared/database/   asyncpg pool and migration runner
└── app.py             application factory
openapi.json           contract artifact; a test asserts byte equality
```

## Tests

75 pass, including integration tests against real PostgreSQL. Security guards are
mutation-tested: each is deleted to confirm a test fails, then restored. Verified
this way are the platform-admin guard, the comment-content leak check, the
owner-only guard, invitation single-use, last-owner protection, and the new pilot
request administration boundary.

## Environment

| Variable | Purpose |
|---|---|
| `DATABASE_URL` | Set from the Render Postgres internal URL |
| `CORS_ORIGINS` | Comma-separated; port-exact, a mismatch gives a 400 preflight |
| `PLATFORM_ADMIN_EMAILS` | Grants Platform Administration at sign-in; reconciled every sign-in so removal revokes |
| `TELEGRAM_BOT_TOKEN` / `TELEGRAM_BOT_USERNAME` | Unset, so Telegram sign-in stays hidden |
| `PUBLIC_FRONTEND_URL` | Base URL used to generate setup and sign-in links |
| `SMTP_HOST`, `SMTP_PORT`, `SMTP_USERNAME`, `SMTP_PASSWORD` | Optional transactional-email connection |
| `SMTP_FROM_EMAIL`, `SMTP_FROM_NAME` | Verified sender identity; required to enable SMTP |

## Operational notes

- `autoDeploy` is enabled but **the GitHub webhook does not fire**. Deploys are
  triggered manually through the Render API.
- A `200` from `/health` during a rollout can still be the previous instance.
  Check the deploy's `finishedAt` before trusting a post-deploy test.
- A local `uvicorn` without `--reload` serves the code it started with. A stale
  process caused a `404` on a route that existed in source.
- cron-job.org pings `/health` to keep the free instance warm. Render's free tier
  allows 750 instance-hours per month; staying awake continuously costs ~730.

## Not yet implemented

Comment context (`post_text` and `parent_text` are nullable and unpopulated) ·
full moderation history endpoint · replaceable ingestion source interface · real
Facebook ingestion · platform administration beyond request review · False
Suppression Rate and Missed Harm Rate.
