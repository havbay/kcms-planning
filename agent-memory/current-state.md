# Current State

**Updated:** 2026-09-07
**Active part:** Multi-Page operations and moderation UI consolidation

KCMS V2 is a working bilingual full-stack prototype. The public experience,
onboarding, identity, isolated Client workspace, overview, moderation Actions,
Corrections, team, settings, and request administration exist. The trained Khmer
model remains future work; the current disclosed engine is PatternMatcher v0.1.

## Current released slice

- Client Page Connection now uses a real product workflow rather than a request
  form: **Continue with Facebook** or an advanced **Page access token**.
- Both methods converge on encrypted workspace records and expose capability
  according to Meta tasks, not according to connection method.
- The Meta application and Render environment are configured. Facebook Login
  URLs include the Business Login configuration id, and the frontend explains
  provider or authorization failures without inventing another approval step.
- Public self-signup is disabled and absent from the contract. New Clients use
  the reviewed pilot request and one-time owner setup link; sign-in routes new
  visitors back to Request access.
- Approved Clients may connect an authorized Facebook Page directly from their
  workspace. Sample-data status is not a Page-connection authorization gate.
- Page Connection supports a connected-Pages collection and per-Page sync and
  disconnect operations. The screen shows connected Pages and their capability
  state before the two connection methods.
- Moderation is a standard compact data table with inline Actions, commenter,
  server-side filters, source-post links/caption/type, stable pagination,
  periodic foreground synchronization, and a complete comment review panel.
- Source and parent context are populated for seeded prototype conversations.
- Actions and Corrections remain separate. HIDE and UNHIDE are mirrored to Meta
  for imported comments; failed provider Actions roll back instead of claiming
  success locally.
- Automated Replies has an owner-controlled Facebook comment demo path. Dry-run
  is the default; live mode replies only to newly synced SAFE comments matching
  an enabled comments rule, records idempotent events, and leaves Messenger
  under development.

## Evidence

- Facebook Login, Page discovery, comment synchronization, and provider-side
  hide/unhide were exercised successfully against a controlled Facebook Page.
- The current backend test run has 44 passing tests and 69 database-dependent
  skips because local PostgreSQL is unavailable. Earlier database-backed suites
  covered workspace isolation, credential protection, and OpenAPI.
- The direct-connection and removed-contract tests failed before the obsolete
  approval gate/routes were removed and pass afterward.
- The merged frontend passes 55 unit tests, strict TypeScript, ESLint with no
  errors and one pre-existing Fast Refresh warning, production build, and 24
  Playwright tests with one intentional skip.
- The teammate's Rules/Profile/Page UI update from PR #6 is integrated on
  `main`. The sign-in card no longer inherits viewport-filling flex growth;
  desktop and mobile screenshots verify the content-sized card and single-row
  mobile header at `c607084`.

## Repository boundary

| Repository | Active branch | Publication state |
|---|---|---|
| `kcms-frontend` | `main` | `c607084`, automatically deployed on Vercel |
| `kcms-backend` | `main` | `6f8ab19`, deployed on Render |
| `kcms-planning` | `main` | this state update pending push |

KCMS v1 remains unchanged and is reference evidence only.

## Remaining work

- Full moderation history and provider event reconciliation.
- Persistent/background synchronization and webhooks; current polling runs only
  while the Moderate screen is open.
- Broader Platform Administration, workspace switching, and valid quality
  metrics.
- Authorized manual Khmer dataset, offline training/evaluation, and a versioned
  model deployment after it beats the baseline safely.
