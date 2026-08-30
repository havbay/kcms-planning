# KCMS V2 Vertical-Slice Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Rebuild KCMS V2 part by part until every MVP user journey works through the approved frontend, versioned API, persistence, authorization, and operational checks.

**Architecture:** The web application and backend are separate repositories joined by a backend-owned OpenAPI contract. Work proceeds as vertical slices, with one active part at a time and a runtime integration gate before completion.

**Tech Stack:** OpenDesign and Markdown for product/design planning; OpenAPI 3.1 for the application contract; frontend and backend runtime stacks are selected and recorded during Part 0 before scaffolding.

**Spec:** `00-product-specification.md`

## Global Constraints

- Preserve every invariant in `00-product-specification.md`.
- Keep `kcms-frontend` and `kcms-backend-` as independent Git repositories.
- Use no production demo routes, fabricated customers, or fabricated analytics.
- Use synthetic fixtures only under test or scripted local-integration ownership.
- Store no credentials or real customer comment content in source or agent memory.
- Build only the active part recorded in `agent-memory/current-state.md`.
- A part is incomplete until its real frontend and backend pass a live journey.
- Record accepted architecture changes under `adr/` before implementation depends on them.

---

## Part 0: Foundation

**Outcome:** Both repositories have approved foundations, shared contracts, CI, local orchestration, and an approved initial OpenDesign system.

- [ ] Confirm or correct the backend directory and GitHub repository name; record the accepted name in `agent-memory/decisions.md` before renaming anything.
- [ ] Install OpenDesign through its supported Ubuntu source path and verify the local Codex MCP without performing optional cloud login.
- [ ] Create the KCMS V2 OpenDesign project and commit the approved `DESIGN.md` export to `kcms-frontend/docs/design/`.
- [ ] Approve `00-product-specification.md`, including roles, taxonomy, invariants, MVP scope, and exclusions.
- [ ] Compare backend runtime candidates using `02-backend-redesign-plan.md` and record the decision in `adr/0002-backend-runtime.md`.
- [ ] Record the frontend runtime and dependency decision in `adr/0003-frontend-runtime.md`.
- [ ] Scaffold the frontend with strict typing, formatting, linting, unit tests, production build, and Playwright.
- [ ] Scaffold the backend with formatting, linting, static typing, unit tests, integration tests, migrations, and OpenAPI generation.
- [ ] Implement backend `GET /api/v1/health` and frontend environment validation.
- [ ] Configure exact local CORS/proxy behavior and a local PostgreSQL service without committing secrets.
- [ ] Add independent CI workflows to both repositories.
- [ ] Add a cross-repository smoke command that starts both projects and verifies frontend, API health, and database connectivity.
- [ ] Capture successful CI/local evidence in all three state files under `agent-memory/`.
- [ ] Mark Part 0 complete only after a clean clone of each repository can pass its documented setup and smoke checks.

## Part 1: Public Access

**Outcome:** A visitor can submit a real access request and receive a safe success response.

- [ ] Produce and approve OpenDesign references for landing, request form, validation, submitting, backend error, and success states in English and Khmer.
- [ ] Add the access-request schema and `POST /api/v1/access-requests` to the backend OpenAPI contract.
- [ ] Write backend tests for valid submission, normalized email, duplicate-safe response, invalid fields, rate limiting, and persistence.
- [ ] Implement the smallest backend behavior that passes those tests and records an auditable request without creating an account.
- [ ] Regenerate the frontend API client from the reviewed OpenAPI document.
- [ ] Write frontend tests for navigation, validation, retained values, submitting state, API error, retry, and success.
- [ ] Implement the landing and request-access experience from the approved references.
- [ ] Add a Playwright journey that submits a unique request and proves it exists through an authorized backend test seam.
- [ ] Verify responsive, keyboard, English, Khmer, console, and horizontal-overflow gates.
- [ ] Record contract revision and runtime evidence, then mark Part 1 complete.

## Part 2: Operator Onboarding

**Outcome:** An authenticated Operator can review a request, create a workspace, and invite its first Administrator.

- [ ] Approve OpenDesign references for Operator sign-in, request list/detail, workspace creation, invitation confirmation, empty, denied, error, and retry states.
- [ ] Define Operator authentication and capability responses in OpenAPI without exposing customer comment content.
- [ ] Define request-list, request-review, workspace-create, and first-Administrator invitation contracts.
- [ ] Write backend authorization tests proving Operators are allowed and customer roles are denied.
- [ ] Write transaction tests proving workspace creation and initial invitation are consistent under retries.
- [ ] Implement audited onboarding services and invitation delivery through a local mail adapter.
- [ ] Regenerate the frontend client and implement the Operator onboarding journey.
- [ ] Add Playwright coverage from Operator sign-in through invitation delivery in the local mail catcher.
- [ ] Verify duplicate submissions and retries do not create duplicate workspaces or invitations.
- [ ] Record runtime evidence and mark Part 2 complete.

## Part 3: Customer Authentication

**Outcome:** The invited Administrator sets a password, signs in, loads `/me`, reaches the customer dashboard, recovers access, and signs out safely.

- [ ] Approve invitation setup, sign-in, recovery, check-email, expired invitation, session-expired, denied, and customer-shell references.
- [ ] Define invitation redemption, password setup, sign-in, recovery request, recovery completion, `/me`, logout, and logout-all contracts.
- [ ] Write backend tests for one-time invitations, expiry, password hashing, rate limiting, session rotation, revocation, CSRF, and capability output.
- [ ] Implement server-side sessions using Secure and HttpOnly production cookies.
- [ ] Write frontend tests for password-manager attributes, paste support, validation, safe redirects, session expiry, and retained recovery values.
- [ ] Implement access screens and the role-capability application shell.
- [ ] Add Playwright coverage from invitation email through authenticated `/me`, logout, and refused post-logout access.
- [ ] Verify an account without workspace access receives the approved no-access state.
- [ ] Record security and runtime evidence and mark Part 3 complete.

## Part 4: Facebook Page Connection

**Outcome:** An Administrator connects a Page, the backend validates the integration through a replaceable adapter, and the frontend displays honest health.

- [ ] Approve Page-connection, credential-entry or OAuth-return, verifying, connected, expired, delayed, and failure references.
- [ ] Define the comment-source port and its scripted local and Facebook adapter contracts in an ADR.
- [ ] Define Page connection, status, disconnect, and reauthorization HTTP contracts without returning provider secrets.
- [ ] Write backend tests for Administrator authorization, encrypted secret storage, adapter validation, status transitions, and disconnect.
- [ ] Implement the scripted local adapter behind the same interface used by Facebook.
- [ ] Implement the customer Page connection UI and health state from real API responses.
- [ ] Add Playwright coverage for scripted connection, health display, refused unauthorized access, and disconnect confirmation.
- [ ] Record Facebook runtime evidence as unverified until a real Graph API handshake is captured separately.
- [ ] Record integration evidence and mark Part 4 complete.

## Part 5: Moderation Workflow

**Outcome:** A comment is ingested, classified, routed, reviewed, acted on, corrected separately, and visible in immutable history.

- [ ] Approve Work List references for Triage, Review, Audit, details, correction, history, action success, empty, stale, partial, and mobile states.
- [ ] Define comment, Verdict, Work List, Action, Correction, and history schemas in OpenAPI.
- [ ] Write backend tests for ingestion idempotency, edit revisions, two-axis Verdicts, abstention, lane routing, ordering, quotas, and audit sampling.
- [ ] Write backend tests proving Actions do not create Corrections and reversals append history.
- [ ] Implement scripted ingestion, classifier port/stub disclosure, routing, Work List, Actions, Corrections, and history.
- [ ] Generate the frontend client and implement lane navigation, context, independent axes, actions, correction disclosure, and history.
- [ ] Add live Playwright journeys for leave, hide, unhide, correction, retry, stale data, and denied access.
- [ ] Verify no institution-directed comment is automatically hidden and automatic hiding is disabled in shadow mode.
- [ ] Record moderation runtime evidence and mark Part 5 complete.

## Part 6: Annotation

**Outcome:** An Annotator blindly labels assigned comments, skips with a reason, sees progress, and can inspect disagreements without model leakage.

- [ ] Approve label-workspace, skip, completion, disagreement, empty, denied, and mobile references.
- [ ] Define assignment, Annotation, skip, progress, disagreement, dataset split, and export schemas.
- [ ] Write backend tests proving Annotators cannot retrieve Verdicts or moderation routes for the same Page.
- [ ] Write tests for assignment stability, split immutability, skips, duplicate submission, progress, and disagreement calculation.
- [ ] Implement blind assignment and annotation services without inferring labels from Actions or Corrections.
- [ ] Implement the frontend label workspace, keyboard accelerators, skip dialog, progress, and disagreements.
- [ ] Add Playwright coverage proving the rendered Annotator network and UI contain no model label or confidence fields.
- [ ] Verify exports preserve training/evaluation separation.
- [ ] Record annotation evidence and mark Part 6 complete.

## Part 7: Administration

**Outcome:** Administrators can understand real operations, manage team access and versioned policy, inspect honest metrics, and export permitted data.

- [ ] Approve Overview, Team, invitation, revocation, Policy, policy history, Metrics, export, and unavailable-data references.
- [ ] Define overview, team, invitation, revocation, policy, policy history, metrics, and export contracts.
- [ ] Write backend tests for last-Administrator protection, one role per person per Page, revocation, policy concurrency, version history, and authorization.
- [ ] Write metric tests for false suppression, missed harm, suppression, disagreement, denominators, contested labels, and surfaced-reason breakdowns.
- [ ] Implement administrative services and truthful unavailable-data responses.
- [ ] Implement the four frontend administration areas using only backend values.
- [ ] Add Playwright coverage for invitation, revocation confirmation, policy conflict, measurable metric, unavailable metric, and export.
- [ ] Verify an Administrator retains moderation capability and lands by capability rather than role name.
- [ ] Record administration evidence and mark Part 7 complete.

## Part 8: Operations And Pilot

**Outcome:** KCMS is observable, security-checked, deployed to staging, running in shadow mode, and ready for a controlled pilot.

- [ ] Approve fleet-health, integration-failure, audit-log, support-confirmation, and pilot-status references.
- [ ] Implement health signals for ingestion delay, credential expiry, job failure, and last successful processing without exposing comment content.
- [ ] Implement append-only audit logs for authentication, onboarding, policy, access, moderation, and support actions.
- [ ] Add structured logs, request IDs, error reporting, database backup/restore validation, and service health checks.
- [ ] Run dependency, secret, authorization, session, CSRF, rate-limit, and cross-workspace security checks.
- [ ] Deploy independently versioned frontend and backend staging services with documented rollback.
- [ ] Connect a controlled Facebook Page and capture an authoritative Graph API ingestion and reversible-action result.
- [ ] Run the classifier in shadow mode and keep automatic hiding disabled.
- [ ] Validate label guidelines and workflow with three to five real Page moderators.
- [ ] Record false-suppression and missed-harm evidence separately, including denominators and contested samples.
- [ ] Resolve pilot-blocking P0/P1 defects and document accepted residual risks.
- [ ] Record staging and pilot evidence and mark Part 8 complete.

## Definition Of Done For Every Part

A part is complete only when all statements below are true:

- [ ] The approved user journey works end to end against the running backend.
- [ ] Data is persisted and can be retrieved after service restart where applicable.
- [ ] Allowed and denied authorization directions are tested.
- [ ] Loading, empty, error, retry, and relevant stale/partial states are usable.
- [ ] English and Khmer responsive interfaces pass the active-slice viewport matrix.
- [ ] Frontend unit, type, build, and Playwright checks pass.
- [ ] Backend unit, integration, migration, lint, format, and type checks pass.
- [ ] The accepted OpenAPI artifact and generated frontend client agree.
- [ ] No production fixture, secret, console error, or unintended overflow is present.
- [ ] Agent memory contains the evidence, decisions, active revision, and next action.
- [ ] Changes are committed and reviewed in each affected repository.

An isolated frontend screen or backend endpoint never satisfies this definition.
