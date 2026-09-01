# Current State

**Updated:** 2026-09-01
**Active part:** Client Page Connection and moderation depth

KCMS V2 is a working bilingual full-stack prototype. The public experience,
onboarding, identity, isolated Client workspace, overview, moderation Actions,
Corrections, team, settings, and request administration exist. The trained Khmer
model remains future work; the current disclosed engine is PatternMatcher v0.1.

## Current released slice

- Client Page Connection now uses a real product workflow rather than a request
  form: **Continue with Facebook** or an advanced **Page access token**.
- Both methods converge on one encrypted workspace record and expose capability
  according to Meta tasks, not according to connection method.
- Moderation is a standard compact data table with server-side filters, source
  post/caption/type, stable pagination, and a complete comment review panel.
- Source and parent context are populated for seeded prototype conversations.
- Actions and Corrections remain separate. Current KCMS Actions are stored
  locally; provider-side hide/unhide is not yet implemented.

## Evidence

- Backend Page Connection, credential, comment filter/context, authorization,
  workspace isolation, and OpenAPI tests pass against PostgreSQL.
- The approved-workspace guard fails its test when deleted and passes when
  restored.
- Frontend unit, type, lint, build, Playwright, and browser layout checks pass.
- No live Meta request was made, so OAuth, synchronization, and provider Actions
  remain explicitly unverified.

## Repository boundary

| Repository | Active branch | Publication state |
|---|---|---|
| `kcms-frontend` | `main` | `516996f`, deployed on Vercel |
| `kcms-backend` | `main` | `b99f780`, deployed on Render |
| `kcms-planning` | `main` | `9b1fa55`, pushed |

KCMS v1 remains unchanged and is reference evidence only.

## Remaining work

- Controlled live Meta authorization, Page discovery, synchronization, and
  reversible hide/unhide proof.
- Full moderation history and provider event reconciliation.
- Broader Platform Administration, workspace switching, and valid quality
  metrics.
- Authorized manual Khmer dataset, offline training/evaluation, and a versioned
  model deployment after it beats the baseline safely.
