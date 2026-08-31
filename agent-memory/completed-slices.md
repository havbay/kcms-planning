# Completed Slices

A slice counts as complete only when its journey works end to end against the
running backend and persistence, and its gates pass.

## Part 0 — Foundation

- React/Vite frontend and FastAPI backend scaffolded, deployed, and reachable.
- `GET /api/v1/health` proven in both states against real PostgreSQL.
- Deterministic OpenAPI artifact with a byte-equality drift test.

## Part 1 — Public site

- Bilingual landing page: hero and Comment Pathway, How KCMS works, Built for
  Khmer, Human control, Early access, footer.
- Sticky header and mobile navigation drawer.
- Every unbuilt route renders a notice page; no path renders blank.

## Part 2 — Authentication

- Email sign-up and sign-in with scrypt; Telegram Login Widget implemented and
  deployed, dormant until a bot token is configured.
- Bearer-token sessions, stored only as a hash.
- Route guard, sign-out, and per-field accessible validation.

## Part 3 — Moderation

- Classifier seam with a disclosed pattern matcher.
- Work list with severity, target, and surfacing reason.
- Actions and reversible history; corrections kept separate from actions.

## Part 4 — Workspaces

- Every account owns an isolated workspace with its own seeded comments.
- Cross-workspace access returns 404 rather than 403.

## Part 5 — Page connection requests

- Client request form with states for pending, approved and declined.
- Platform Administration review with approve and decline-with-reason.
- Administration responses provably carry no comment content.

## Part 6 — Team and settings

- Membership with owner and member roles.
- Single-use invitation links with a public preview and a seven-day expiry.
- Last-owner protection; workspace and personal rename.

## Part 7 — Operational density

- Moderation rebuilt as a paginated table with an expandable detail row.
- Application type scale scoped to the dashboard, separate from marketing.
- Workspace summary computed in the database rather than from a page.

## Verification at the time of writing

68 backend tests, 19 frontend unit tests, 22 Playwright tests. Strict TypeScript,
ESLint, Vite production build, ruff. Security guards mutation-tested.
