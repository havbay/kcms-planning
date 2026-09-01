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
- The Meta application and Render environment are configured. Facebook Login
  URLs include the Business Login configuration id, and the frontend explains
  provider or authorization failures without inventing another approval step.
- Public self-signup is disabled and absent from the contract. New Clients use
  the reviewed pilot request and one-time owner setup link; sign-in routes new
  visitors back to Request access.
- Approved Clients may connect an authorized Facebook Page directly from their
  workspace. Sample-data status is not a Page-connection authorization gate.
- Moderation is a standard compact data table with server-side filters, source
  post/caption/type, stable pagination, and a complete comment review panel.
- Source and parent context are populated for seeded prototype conversations.
- Actions and Corrections remain separate. Current KCMS Actions are stored
  locally; provider-side hide/unhide is not yet implemented.

## Evidence

- All 77 backend tests pass against PostgreSQL, including direct authenticated
  Client Page Connection, invite-only account creation, credential protection,
  comment context, workspace isolation, and OpenAPI.
- The direct-connection and removed-contract tests failed before the obsolete
  approval gate/routes were removed and pass afterward.
- All 34 frontend unit tests, strict TypeScript, lint without errors, production
  build, and 24 Playwright tests with one intentional skip pass.
- The configuration-aware backend and frontend are live. A successful Meta
  consent callback has not yet been observed, so Page discovery,
  synchronization, and provider Actions remain explicitly unverified.

## Repository boundary

| Repository | Active branch | Publication state |
|---|---|---|
| `kcms-frontend` | `main` | `a2c6d44`, deployed on Vercel |
| `kcms-backend` | `main` | `4b5addb`, deployed on Render |
| `kcms-planning` | `main` | this state update pending push |

KCMS v1 remains unchanged and is reference evidence only.

## Remaining work

- Controlled live Meta authorization, Page discovery, synchronization, and
  reversible hide/unhide proof.
- Full moderation history and provider event reconciliation.
- Broader Platform Administration, workspace switching, and valid quality
  metrics.
- Authorized manual Khmer dataset, offline training/evaluation, and a versioned
  model deployment after it beats the baseline safely.
