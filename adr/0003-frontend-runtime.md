# React And Vite Frontend Runtime

**Status:** accepted

**Date:** 2026-08-30

## Context

KCMS V2 needs a bilingual, responsive operational interface with strict contract
handling, accessible repeated-work controls, fast component feedback, and real
browser proof. The owner accepted frontend-first delivery: an approved Client
prototype is implemented before matching backend behavior, then connected to the
live API before a roadmap part can complete.

Frontend-first must not create a second contract or let fabricated data enter
production. OpenDesign handoff precedes production screens, OpenAPI defines the
network boundary, and simulation is isolated to tests and an explicitly enabled
local preview.

## Decision

KCMS V2 uses React 19 with TypeScript strict and Vite on Node.js 22 or newer.

- npm owns dependencies and the committed package-lock.json.
- TypeScript enables strict, noUncheckedIndexedAccess,
  exactOptionalPropertyTypes, and useUnknownInCatchVariables.
- Vite owns development and production builds.
- Vitest and Testing Library own unit and component behavior tests.
- Playwright owns browser, accessibility, console, responsive, and live
  cross-repository journeys.
- The backend-owned OpenAPI 3.1 JSON artifact is the only source for generated API
  types and client operations. A package script regenerates the client and a
  clean-tree check detects drift.
- Runtime configuration is parsed once at startup. The API base URL must be an
  absolute http or https URL; invalid configuration renders a safe configuration
  error without issuing a request.
- MSW may intercept requests in Vitest, Playwright prototype tests, and an
  explicitly enabled local preview entry point. Production entry points and
  feature modules never import MSW handlers, fixtures, or fake customer records.
- The first implementation is an approved Client shell and honest connection/work
  summary workflow. It discloses the active versioned pattern matcher and never
  presents a moderation Action as automated.

## Alternatives Considered

### Next.js

Next.js offers integrated routing, server rendering, and full-stack conventions.
It was not selected because KCMS has an independent FastAPI backend, the primary
experience is an authenticated operational application, and server-component or
server-action boundaries would add runtime concepts without a Part 0 requirement.
A static public acquisition site can be reconsidered separately if measured SEO
or rendering requirements justify it.

### Vue or Svelte

Both can produce accessible, fast applications with smaller conceptual surfaces.
React 19 was selected because the accepted implementation direction, testing
ecosystem, OpenDesign-to-component workflow, and available team familiarity reduce
delivery risk. This is a staffing/tooling tradeoff rather than a claim that React
is universally superior.

### pnpm or Yarn

Both provide capable deterministic installs and workspace features. npm is chosen
because there is one frontend package, Node 22 ships it, and it avoids an
additional package-manager bootstrap. The committed lock file remains mandatory.

### Handwritten API interfaces

Handwritten request/response types are initially quicker but drift silently across
independent repositories. They are rejected. UI view models may wrap generated
transport types, but may not redefine the HTTP contract.

### Importing fixtures into the application during frontend-first work

Direct fixture imports would make demos easy but could leak fabricated customer
data into production bundles and mask the real network lifecycle. Network-level
simulation is selected so the production code still exercises generated client,
loading, error, retry, and response parsing paths.

## Consequences

- Frontend behavior can be reviewed early without waiting for backend
  implementation.
- The approved OpenDesign artifact and OpenAPI contract become hard prerequisites
  for production screen work.
- Preview startup needs an explicit simulation flag and separate dynamic import.
- The team pays a client-regeneration step on contract changes but avoids duplicate
  transport types.
- A simulated browser journey is prototype evidence only. Completion still
  requires the live FastAPI/PostgreSQL path.
- Node 22 or newer and npm are required in developer and CI environments.

## Verification

This ADR records an accepted planning decision. No React application, generated
client, MSW boundary, build, component test, or Playwright journey was executed by
this planning-only change. Those claims remain unverified until the Part 0 plan is
implemented and its commands pass.
