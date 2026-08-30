# KCMS V2 Vertical-Slice Implementation Roadmap

> **For agentic workers:** REQUIRED SUB-SKILL: Use
> superpowers:subagent-driven-development (recommended) or
> superpowers:executing-plans to implement an accepted exact plan task-by-task.
> Steps use checkbox syntax for tracking.

**Goal:** Deliver the initial KCMS V2 Facebook Page comment-moderation MVP through
an approved frontend-first prototype, a versioned backend API, persistence,
authorization, and live cross-repository proof.

**Architecture:** kcms-frontend and kcms-backend are separate repositories. The
backend owns the OpenAPI 3.1 contract and the frontend consumes an accepted
artifact. Before the backend exists, frontend development may simulate that exact
contract only at the network boundary; production modules never import fixtures
or fake customer data.

**Tech Stack:** OpenDesign; React 19, TypeScript strict, Vite, npm, Vitest,
Testing Library, Playwright; FastAPI on Python 3.12 or newer, uv, Pydantic,
asyncpg/PostgreSQL; deterministic OpenAPI 3.1.

**Spec:** 00-product-specification.md

## Global Constraints

- Preserve every invariant in 00-product-specification.md.
- Expose only Client and Platform Administrator as authenticated MVP roles.
- Keep kcms-frontend and kcms-backend as independent Git repositories.
- Begin frontend implementation only after the active screen states have an
  accepted OpenDesign handoff.
- Define frontend API behavior from the accepted OpenAPI contract.
- Simulate API behavior only through network interception in tests and an
  explicitly enabled local preview; production modules never import fixtures.
- Use no production demo routes, fabricated customers, or fabricated analytics.
- Build only the active part recorded in agent-memory/current-state.md.
- Implement frontend behavior before the matching backend behavior, then replace
  simulation with the real API before the part can complete.
- A part is incomplete until its real frontend and backend pass a live journey.
- Keep every prototype Facebook Action human-initiated; do not implement automatic
  replies, Messenger automation, or additional providers in the initial MVP.
- Record accepted architecture changes under adr/ before implementation depends
  on them.

---

## Part 0: Frontend-First Walking Skeleton

**Outcome:** An approved, usable Client prototype shell shows honest connection
and work-summary states through the versioned health boundary; the subsequent
FastAPI service proves PostgreSQL-aware health and replaces the frontend's network
simulation in one sibling-repository smoke check.

- [ ] Accept the product, role, provider, human-action, repository-name, and
  frontend-first decisions in planning memory.
- [ ] Accept the backend and frontend runtime ADRs.
- [ ] Approve the OpenDesign handoff for the Client shell, pattern-matcher
  disclosure, connection health, work summary, loading, unavailable, and retry
  states before creating production screens.
- [ ] Scaffold kcms-frontend with React 19, TypeScript strict, Vite, npm, Vitest,
  Testing Library, Playwright, lint, formatting, and production build checks.
- [ ] Commit the backend-owned Part 0 OpenAPI artifact to the frontend contract
  input boundary and generate the frontend client from it.
- [ ] Develop the usable Client shell and health/summary workflow against MSW
  network interception in tests and explicitly enabled local preview only.
- [ ] Prove production entry points do not import preview handlers, fixtures, or
  fabricated customer data.
- [ ] Scaffold kcms-backend only after the frontend prototype passes its gates.
- [ ] Implement database-aware GET /api/v1/health, deterministic OpenAPI export,
  and PostgreSQL Docker Compose through backend RED/GREEN tests.
- [ ] Replace frontend preview simulation with the live backend and run one
  sibling-repository smoke command proving frontend, API, and database health.
- [ ] Record only the test/runtime evidence actually produced.
- [ ] Mark Part 0 complete only after clean-clone setup and the live smoke command
  pass; source inspection or simulated responses alone are insufficient.

Write and accept a bite-sized frontend-first execution plan before changing either
application repository.

## Part 1: Public Access

**Outcome:** A visitor can use the approved public experience to submit a real
access request and receive a safe success response.

- [ ] Approve landing and request-access references in English and Khmer,
  including validation, submitting, unavailable, retry, and success states.
- [ ] Define POST /api/v1/access-requests in the backend-owned OpenAPI artifact.
- [ ] Implement and test the frontend workflow first using contract-faithful
  network interception outside production modules.
- [ ] Implement backend normalization, duplicate-safe behavior, rate limiting,
  persistence, and audit history through failing tests.
- [ ] Regenerate the frontend client and remove reliance on simulation for the live
  journey.
- [ ] Prove the responsive, keyboard, localization, error, and persisted live path.
- [ ] Record contract revision and runtime evidence, then mark Part 1 complete.

## Part 2: Platform Administration Onboarding

**Outcome:** A Platform Administrator can review an access request, create a
Client workspace, and invite its first Client user without ordinary access to
customer comment content.

- [ ] Approve Platform Administrator sign-in, request list/detail, workspace
  creation, invitation confirmation, empty, denied, error, and retry references.
- [ ] Define capability, request-review, workspace-create, and Client invitation
  contracts without customer comment payloads.
- [ ] Implement and test the frontend journey first at the network boundary.
- [ ] Write backend allowed/denied tests proving Platform Administrators are
  allowed and Clients are denied platform-wide onboarding operations.
- [ ] Implement audited onboarding and a local mail adapter with retry-safe
  transactions.
- [ ] Regenerate the client and prove the live journey through the local mail
  catcher without duplicate workspaces or invitations.
- [ ] Record runtime evidence and mark Part 2 complete.

## Part 3: Client Authentication And Honest Summary

**Outcome:** The invited Client sets a password, signs in, loads /me, reaches a
simple honest Client dashboard, recovers access, and signs out safely.

- [ ] Approve invitation, sign-in, recovery, session-expired, denied, Client shell,
  and honest unavailable-summary references.
- [ ] Define invitation redemption, password setup, sign-in, recovery, /me,
  logout, logout-all, and summary contracts.
- [ ] Implement frontend auth and summary states first without fabricated metrics.
- [ ] Write backend tests for invitation expiry, password hashing, rate limiting,
  session rotation/revocation, CSRF, workspace isolation, and role capabilities.
- [ ] Implement server-side sessions using Secure and HttpOnly production cookies.
- [ ] Regenerate the client and prove invitation through authenticated summary,
  logout, and refused post-logout access against the running backend.
- [ ] Record security and runtime evidence and mark Part 3 complete.

## Part 4: Facebook Page Connection

**Outcome:** A Client connects one Facebook Page through a replaceable source
adapter and sees honest integration health.

- [ ] Approve connection, verification, connected, expired, delayed, disconnect,
  and failure references.
- [ ] Define and accept the comment-source port ADR and connection/status contract.
- [ ] Implement the frontend connection journey first with network-level
  simulation and no provider secret in browser-visible data.
- [ ] Write backend tests for Client capability, workspace isolation, encrypted
  secret storage, adapter validation, status transitions, and disconnect.
- [ ] Implement scripted local and Facebook adapters behind the same port.
- [ ] Prove scripted connection, health, denied access, and disconnect live; leave
  real Graph API status explicitly unverified until an authoritative handshake.
- [ ] Record integration evidence and mark Part 4 complete.

## Part 5: Human Moderation Workflow

**Outcome:** A Facebook Page comment is ingested, classified by the disclosed
versioned pattern matcher, routed, reviewed by a Client, acted on only by that
human, corrected separately, and visible in append-only history.

- [ ] Approve Triage, Review, Audit, detail, Action, Correction, history, empty,
  stale, partial, disclosure, and mobile references.
- [ ] Define comment, Verdict, Work List, Action, Correction, and history schemas.
- [ ] Implement the frontend workflow first with the pattern-matcher name/version
  visible and every provider Action behind an explicit Client control.
- [ ] Write backend tests for ingestion idempotency, revisions, two-axis Verdicts,
  abstention, routing, ordering, and audit sampling.
- [ ] Write invariant tests proving Actions never create Corrections, reversals
  append history, and no Facebook Action can originate from automatic routing.
- [ ] Implement scripted ingestion, versioned pattern matcher, routing, Work List,
  human Actions, Corrections, and history.
- [ ] Regenerate the client and prove leave, hide, unhide, Correction, retry,
  stale-data, and denied-access journeys against the running backend.
- [ ] Record moderation runtime evidence and mark Part 5 complete.

## Part 6: Client Workspace Management

**Outcome:** A Client manages trusted teammates and versioned Page Policy and sees
a simple operational summary using only real, available backend measures.

- [ ] Approve Team, invitation, revocation, Policy/history, summary, unavailable,
  and conflict references.
- [ ] Define team, invitation, revocation, policy, policy history, and operational
  summary contracts.
- [ ] Implement frontend workflows first with processed, surfaced, reviewed,
  pending, review time, moderation outcomes, and connection health only.
- [ ] Write backend tests for last-Client protection, workspace isolation,
  revocation, policy concurrency, version history, and honest denominators.
- [ ] Implement services without treating hiding as success or inventing accuracy.
- [ ] Regenerate the client and prove invitation, revocation, policy conflict,
  available measures, and unavailable states live.
- [ ] Record runtime evidence and mark Part 6 complete.

## Part 7: Platform Operations

**Outcome:** Platform Administrators can understand service health, support Client
accounts, and inspect audited platform activity without browsing customer comment
content.

- [ ] Approve fleet health, integration failure, Client support, confirmation,
  audit-log, empty, denied, and retry references.
- [ ] Define platform health, workspace/user support, and support-audit contracts
  that exclude ordinary customer comment content.
- [ ] Implement the frontend operations experience first at the network boundary.
- [ ] Write backend authorization, privacy-boundary, attribution, and append-only
  audit tests.
- [ ] Implement health signals for ingestion delay, credential expiry, job failure,
  and last successful processing plus audited account support.
- [ ] Regenerate the client and prove allowed, denied, content-exclusion, and audit
  journeys live.
- [ ] Record runtime evidence and mark Part 7 complete.

## Part 8: Staging And Controlled Pilot

**Outcome:** KCMS is observable, security-checked, deployed to staging, running
with human-only Actions, and ready for a controlled Facebook Page pilot.

- [ ] Add structured logs, request IDs, error reporting, backup/restore validation,
  service health, and documented rollback.
- [ ] Run dependency, secret, authorization, session, CSRF, rate-limit, and
  cross-workspace security checks.
- [ ] Deploy independently versioned frontend and backend staging services.
- [ ] Connect a controlled Facebook Page and capture authoritative Graph API
  ingestion plus a reversible Action explicitly initiated by a Client.
- [ ] Keep automatic Facebook Actions, public replies, Messenger automation, and
  other providers disabled and absent from the initial MVP.
- [ ] Validate the primary workflow with three to five real Client users.
- [ ] Record false-suppression and missed-harm evidence separately when labelled
  denominators exist; keep unavailable measures honest.
- [ ] Resolve pilot-blocking defects, document residual risks, record runtime
  evidence, and mark Part 8 complete.

## Later Non-Blocking Track: Internal Dataset And Khmer Model

This internal track starts only after the functional prototype can serve the
initial MVP and never blocks Parts 0-8.

- [ ] Accept a written Khmer/Khmerlish labelling guideline, authorization basis,
  privacy policy, retention policy, and dataset lineage design.
- [ ] Manually label an authorized seed dataset and keep evaluation samples out of
  training data.
- [ ] Curate explicit Client Corrections as candidates; never infer labels from
  moderation Actions and never expose a client Annotation workspace for this work.
- [ ] Train and evaluate a Khmer model offline against accepted gates.
- [ ] Require explicit version approval, shadow comparison, deployment record, and
  rollback before replacing the versioned pattern matcher.
- [ ] Never retrain or change a production model automatically.

## Definition Of Done For Every Part

A part is complete only when all statements below are true:

- [ ] The approved user journey works end to end against the running backend.
- [ ] Data is persisted and retrievable after service restart where applicable.
- [ ] Allowed and denied authorization directions are tested.
- [ ] Loading, empty, error, retry, and relevant stale/partial states are usable.
- [ ] English and Khmer responsive interfaces pass the active-slice viewport matrix.
- [ ] Frontend unit, type, lint, build, and Playwright checks pass.
- [ ] Backend unit, integration, migration, lint, format, and type checks pass.
- [ ] The accepted OpenAPI artifact and generated frontend client agree.
- [ ] No production fixture, fake customer data, secret, console error, or
  unintended overflow is present.
- [ ] Agent memory contains evidence labelled source-confirmed, test-confirmed,
  runtime-confirmed, or unverified.
- [ ] Changes are committed and reviewed in each affected repository.

An approved prototype driven by network simulation is useful frontend evidence,
but it never substitutes for the live backend and persistence completion gate.
