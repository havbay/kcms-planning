# Current State

**Updated:** 2026-08-31

**Active part:** Part 0 - Foundation

**Part status:** Frontend foundation in progress. The first public header/hero
slice is implemented and verified; backend and live integration have not started.

## Repository State

- `kcms-planning`: planning-only worktree on `feature/v2-foundation`; no remote is
  configured.
- `kcms-frontend`: React/Vite scaffold and bilingual landing header/hero are on
  `feature/landing-header-hero` in the canonical V2 folder; not pushed or merged.
- `kcms-backend`: no application scaffold; agent workflow committed on `main` at
  `85dbfdf`; the foundation worktree is on `feature/v2-foundation` and its local
  `origin` targets the canonical name.
- KCMS v1: unchanged on `feature/kcms-full-redesign-bilingual`; it is reference
  evidence and not the V2 implementation base.

## Confirmed Evidence

- `source-confirmed`: both V2 code repositories contain only their root agent
  workflow and Git metadata; no runtime code exists.
- `source-confirmed`: frontend and backend use independent Git repositories.
- `source-confirmed`: `kcms-backend` is the accepted repository name and the local
  foundation-worktree remote targets it.
- `source-confirmed`: read-only GitHub verification confirmed
  `havbay/kcms-backend` as canonical and the legacy URL redirects to it; no push
  was performed.
- `source-confirmed`: `DESIGN.md` contains the required ten-section KCMS design
  outline; its unspecified design decisions remain `TBD` and it is not approved.
- `runtime-confirmed`: Codex CLI is `0.151.0`.
- `runtime-confirmed`: OpenDesign uses isolated Node `24.18.0` and pnpm
  `10.33.2`; the workstation default Node installation was not changed.
- `runtime-confirmed`: OpenDesign `0.21.1` runs from upstream `main` commit
  `df84ae5` with web on `127.0.0.1:5180` and daemon on `127.0.0.1:7456`.
- `runtime-confirmed`: the official OpenDesign Codex plugin `0.5.3` is installed
  and enabled, and its registered MCP completed a 22-tool handshake.
- `runtime-confirmed`: OpenDesign's live Claude Code catalog includes Claude 5
  aliases and explicit Opus 5, Sonnet 5, and Fable 5 options.
- `source-confirmed`: the accepted prototype direction uses two visible
  authenticated roles: Client and Platform Administrator.
- `source-confirmed`: the immediate goal is a real full-stack prototype using a
  disclosed, versioned pattern-matching classifier with human moderation Actions.
- `source-confirmed`: a trained Khmer model follows a separately governed manual
  seed-dataset, offline evaluation, approval, and versioned deployment process.
- `source-confirmed`: accepted ADRs choose FastAPI/Python 3.12+/uv/PostgreSQL for
  the backend and React 19/TypeScript strict/Vite/Node 22+/npm for the frontend.
- `source-confirmed`: execution is frontend-first after an approved design
  handoff and accepted OpenAPI artifact; simulation is restricted to the network
  boundary and never satisfies the live completion gate.
- `source-confirmed`: D-017 accepts direct Codex UX/web-design handoffs and makes
  OpenDesign generation optional after explicit product-owner approval.
- `runtime-confirmed`: Vitest 5/5, Playwright 3/3, strict TypeScript, ESLint, Vite
  build, and desktop/mobile rendered checks pass for the landing header/hero.

## Remaining Preconditions And External Work

- Review the implemented landing header/hero before expanding to the next public
  landing section.
- Choose and configure a remote for `kcms-planning` separately.
- Application implementation and runtime proof have not started.

## Last Verified Outcome

The accepted product direction, frontend-first roadmap, repository name, runtime
ADRs, and API health semantics are planning-source evidence. The exact Part 0
implementation plan remains the next planning deliverable.
Earlier OpenDesign tool integration remains runtime-confirmed. No React, FastAPI,
PostgreSQL, generated-client, or live cross-repository behavior is claimed.
