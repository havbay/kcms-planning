# Current State

**Updated:** 2026-08-31

**Active part:** Part 1 - Client workspace

**Part status:** The full stack is live. Landing page, authentication, isolated
client workspaces, the moderation work list, actions, and corrections all run
against a deployed backend and PostgreSQL. Platform Administration and the
remaining client workspace screens have not started.

## Repository State

All three repositories are on GitHub under `havbay` and are pushed.

| Repository | Branch | Live |
|---|---|---|
| `kcms-frontend` | `main` | https://kcms-frontend.vercel.app |
| `kcms-backend` | `main` | https://kcms-backend.onrender.com |
| `kcms-planning` | `main` | — |

- `kcms-frontend`: deployed on Vercel. `vercel deploy --prod` publishes.
- `kcms-backend`: deployed on Render (Singapore, free plan) with a free
  PostgreSQL 16 instance. `autoDeploy` is enabled but the GitHub webhook is not
  firing, so deploys are currently triggered manually.
- KCMS v1: unchanged. Reference evidence, not the V2 implementation base.

## What Is Implemented

**Public**
- Bilingual landing page: hero and Comment Pathway, How KCMS works, Built for
  Khmer, Human control, Early access, footer.
- Sticky header, mobile navigation drawer, English/Khmer switching throughout.
- `/request-access`, `/contact`, `/privacy` and unknown routes render notice
  pages, never a blank screen.

**Authentication**
- Email and password sign-up and sign-in with scrypt hashing.
- Telegram Login Widget implemented and deployed; it stays hidden until
  `TELEGRAM_BOT_TOKEN` and `TELEGRAM_BOT_USERNAME` are configured.
- Sessions are bearer tokens, stored only as a SHA-256 hash.
- Identity is modelled per provider, so an account can hold both.

**Client workspace**
- Every account owns an isolated sandbox workspace, seeded with its own copy of
  the sample Khmer comments.
- Overview: comments processed, need review, reviewed, pending, a surfaced-reason
  breakdown, and moderation outcomes. Every figure derives from real data.
- Moderate: the work list with severity, target, why each comment surfaced,
  Leave/Hide/Unhide, and reversible history.
- Corrections: a human states what the labels should be, without acting on the
  comment.

**Backend**
- `GET /api/v1/health`, `GET /api/v1/comments`,
  `POST /api/v1/comments/{id}/actions`, `POST /api/v1/comments/{id}/corrections`,
  and the `/api/v1/auth/*` surface.
- Deterministic OpenAPI artifact with a byte-equality drift test.
- Forward-only SQL migrations applied at startup.

## Confirmed Evidence

- `runtime-confirmed`: 31 backend tests pass, including seven integration tests
  against real PostgreSQL.
- `runtime-confirmed`: 19 frontend unit tests and 14 Playwright tests pass, with
  strict TypeScript, ESLint, and the Vite production build.
- `runtime-confirmed`: workspace isolation verified in production. Two fresh
  accounts receive disjoint comment ids; neither sees the other's actions; acting
  on another workspace's comment id returns 404, not 403, so existence is not
  disclosed.
- `runtime-confirmed`: hiding a comment writes no Correction, and submitting a
  Correction does not act on the comment. Both are asserted against real
  PostgreSQL.
- `runtime-confirmed`: institution-directed criticism never routes to `triage`.
  A substring collision (`វា` inside `សេវា`) previously mislabelled service
  complaints as person-directed; it is fixed and regression-tested.
- `runtime-confirmed`: unrecognised Khmer abstains as `novel_language` rather
  than being cleared as safe.
- `runtime-confirmed`: moderation endpoints return 401 without a session, and
  actions are attributed to the signed-in person.
- `source-confirmed`: two visible authenticated roles remain Client and Platform
  Administrator.
- `source-confirmed`: the classifier seam is in place; swapping the pattern
  matcher for a trained model changes one line.

## Remaining Preconditions And External Work

- The GitHub account is billing-locked, so GitHub Actions cannot run. The
  `keep-warm` workflow is committed and blocked on this.
- No uptime ping is configured. The Render free plan sleeps after roughly fifteen
  minutes, and the first request then takes thirty to sixty seconds.
- The Render GitHub webhook is not firing; pushes to `main` do not auto-deploy.
- `TELEGRAM_BOT_TOKEN` and `TELEGRAM_BOT_USERNAME` are unset, so Telegram
  sign-in is dormant.
- Real Facebook ingestion is untested.

## Not Yet Implemented

- Client workspace: Page connection, Team, Settings, Policy, comment review
  detail with post and parent context, full history, and metrics with
  denominators.
- Public: request access as a stored record, invitation setup, recovery.
- Platform Administrator: nothing. Access requests, workspace onboarding, user
  support, fleet health, integration health, audited support actions.
- A replaceable ingestion source interface. The classifier seam exists; the
  ingestion port does not.
- False Suppression Rate and Missed Harm Rate.
