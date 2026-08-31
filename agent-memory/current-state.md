# Current State

**Updated:** 2026-08-31

**Active part:** Part 8 - Depth in the moderation workflow

**Part status:** The full stack is live. The client dashboard is complete with no
placeholder sections: Overview, Moderate, Page connection, Team and Settings all
run against a deployed backend and PostgreSQL. Platform Administration exists for
access requests only. Remaining work is depth in the moderation workflow, the
rest of Platform Administration, and real Facebook ingestion.

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
- Moderate: a paginated table with severity, target, why each comment surfaced,
  Leave/Hide/Unhide inline, and an expandable detail row carrying confidences,
  model version, rationale and correction.
- Corrections: a human states what the labels should be, without acting on the
  comment.
- Page connection: a sandbox workspace requests a real Page; a Platform
  Administrator approves or declines with a reason.
- Team: membership with owner and member roles, and single-use invitation links.
- Settings: workspace rename (owner only) and personal display name (anyone).

**Platform Administration**
- Access requests: list, approve, decline with a reason. The response provably
  carries no comment content at any nesting level.

**Backend**
- Health, paginated comments, a database-computed workspace summary, actions,
  corrections, access requests, administration decisions, team, settings, and
  the `/api/v1/auth/*` surface. See `backend-state.md`.
- Deterministic OpenAPI artifact with a byte-equality drift test.
- Forward-only SQL migrations applied at startup.

## Confirmed Evidence

- `runtime-confirmed`: 68 backend tests pass, including integration tests against
  real PostgreSQL.
- `runtime-confirmed`: 19 frontend unit tests and 22 Playwright tests pass, with
  strict TypeScript, ESLint, and the Vite production build.
- `runtime-confirmed`: security guards are mutation-tested. Deleting the
  platform-admin guard, the comment-content leak check, the owner-only guard,
  invitation single-use, or last-owner protection each fails a test.
- `runtime-confirmed`: the work list paginates with a deterministic sort. Without
  a `comment_id` tiebreaker, rows sharing a timestamp appeared on more than one
  page and others never appeared at all.
- `runtime-confirmed`: the Overview summary is computed in the database, not
  derived from a page of results, which would have under-reported once the list
  was paginated.
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

- The Render GitHub webhook is not firing; pushes to `main` do not auto-deploy
  and deploys are triggered manually through the API.
- The GitHub account is billing-locked, so GitHub Actions cannot run. The
  keep-warm workflow was removed in favour of an external ping.
- cron-job.org pings `/api/v1/health` to keep the free instance warm.
- `TELEGRAM_BOT_TOKEN` and `TELEGRAM_BOT_USERNAME` are unset, so Telegram
  sign-in is dormant.
- Real Facebook ingestion is untested.

## Not Yet Implemented

- Comment context. `post_text` and `parent_text` exist in `comment_content` but
  nothing populates them.
- Work list filtering and full moderation history.
- Platform Administration beyond access requests: workspaces, users, fleet
  health, audit log.
- Page Policy, and metrics with denominators. False Suppression Rate and Missed
  Harm Rate do not exist.
- A replaceable ingestion source interface. The classifier seam exists; the
  ingestion port does not. Real Facebook ingestion is untested.
- A workspace switcher. Someone who belongs to two workspaces cannot return to
  their sandbox.
- `/admin/requests` still uses the card layout and no longer matches the density
  of the moderation table.
