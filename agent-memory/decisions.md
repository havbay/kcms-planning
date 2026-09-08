# Accepted Decisions

## D-001: Greenfield KCMS V2

KCMS V2 is a new implementation. KCMS v1 is read-only reference evidence; code is
ported only when the V2 specification explicitly requires and tests the behavior.

## D-002: Separate Application Repositories

Frontend and backend use independent repositories, CI, dependencies, and releases.
See `adr/0001-separate-frontend-and-backend-repositories.md`.

## D-003: Canonical Planning Memory

Shared product requirements, architecture decisions, implementation order, and
handoff state live in the sibling `kcms-planning` repository.

## D-004: Vertical Slices

Work proceeds through Parts 0-8, one active part at a time. A frontend-only screen
or backend-only endpoint is not a completed part.

## D-005: Design Handoff Before Frontend Implementation

Every frontend slice receives an approved design handoff before production UI
implementation begins. D-017 permits a direct Codex UX/web-design handoff;
OpenDesign output, when used, does not define backend behavior.

## D-006: Real Production Data Boundaries

Production frontend code renders backend responses. Synthetic data is restricted
to tests, scripted local integration adapters, and clearly labelled design artifacts.

## D-007: Domain Invariants Survive The Rebuild

The two-axis taxonomy and separation of Verdicts, Actions, Corrections, and
Annotations remain mandatory regardless of backend technology.

## D-008: Two Visible MVP Roles

The authenticated MVP exposes one Client role and one Platform Administrator
role. Every trusted Client user initially receives workspace management and
moderation capabilities. The backend still represents individual capabilities so
a restricted client role can be added later without redesigning authorization.

## D-009: Functional Full-Stack Prototype First

The immediate delivery goal is a working frontend and backend with persistence,
authorization, a usable Client dashboard, ingestion, routing, human moderation,
and history. An isolated design, frontend, API, or model experiment does not
satisfy this goal.

## D-010: Versioned Pattern Matching Before A Trained Model

The prototype classifier is a deterministic, explicitly disclosed pattern-matching
implementation behind the replaceable classifier interface. It may classify,
prioritize, and route comments, but every Facebook moderation Action remains a
human decision throughout the prototype and initial shadow mode.

## D-011: Manual-First Khmer Model Development

Before introducing a trained Khmer model, the team creates an authorized manually
labelled seed dataset and a held-out evaluation set using an accepted labelling
guideline. Later client Corrections are curated candidates, not automatic training
inputs. Actions are never inferred as labels, evaluation data never enters
training, and production models never retrain automatically.

## D-012: Focused Initial Provider Scope

The initial provider scope is Facebook Page comment ingestion and human moderation.
Public auto-replies, Messenger automation, Instagram, and other providers remain
future extensions and must not block the functional prototype.

## D-013: Simple Client Performance Summary

The Client dashboard prioritizes connection health, comments processed, comments
surfaced, comments reviewed, pending work, review time, and moderation outcomes.
It does not treat hiding as success or display unsupported model-accuracy or
marketing analytics.

## D-014: Canonical Backend Repository Name

The accepted application repository name is `kcms-backend`, without a trailing
hyphen. The local remote configuration targets that canonical name. Read-only
GitHub verification confirmed `havbay/kcms-backend` as canonical and the legacy
URL redirects to it; no push or network-side mutation is claimed.

## D-015: Accepted Application Runtimes

The backend is a FastAPI modular monolith on Python 3.12 or newer with uv,
Pydantic, asyncpg/PostgreSQL, and deterministic OpenAPI. The frontend is React 19
with TypeScript strict and Vite on Node 22 or newer with npm, Vitest, Testing
Library, Playwright, and a generated OpenAPI client. See ADRs 0002 and 0003.

## D-016: Frontend-First Contract Delivery

Each product slice begins with an accepted design handoff and an accepted
backend-owned OpenAPI artifact. The frontend behavior is implemented first. API
responses may be simulated only by network interception in tests and an explicitly
enabled local preview; production entry points and feature modules never import
fixtures or fake customer data. The matching backend follows, and live frontend,
API, and persistence proof is still required before the slice completes.

## D-017: Direct Codex Design Handoff

The product owner explicitly selected direct Codex design after approving the UX
flow, landing structure, palette, copy direction, and reference strategy. An
approved direct-Codex UX/web-design brief may satisfy the pre-code design gate;
OpenDesign generation is optional rather than mandatory. The first accepted
direct handoff is the bilingual landing-page header and hero in
`kcms-frontend/docs/design/part-0-landing-header-hero/`.

## D-018: Open Sign-Up Into A Sandbox Workspace

The MVP scope originally excluded public self-service signup, and the canonical
journey began with a reviewed access request. That is superseded.

Anyone may create an account. Each account owns an isolated sandbox workspace
seeded with sample Khmer comments. Connecting a real Facebook Page remains gated
on Platform Administrator approval.

The reason is that Meta keeps the app in development mode, so the Graph API only
works on Pages where one person holds both Page-admin and app-admin roles. Until
App Review, a stranger cannot connect their own Page whatever the product
promises. Gating sign-up would therefore block evaluation without protecting
anything.

Sign-up is open; capability is gated. The `workspace.is_sandbox` flag is what the
gate reads. The reviewed-access-request path is still required before a workspace
may connect a real Page.

## D-019: Seeded Sample Comments Are Accepted In The Prototype

The specification excludes fabricated sample customer records, and the frontend
plan prohibits runtime sample data. Both remain true of the frontend: production
entry points import no fixtures, and every figure shown is computed from API
responses.

The backend seeds each new workspace with hand-written Khmer comments. They are
authored examples, never real user content, and they exist to exercise the
contrast the product turns on: targeted abuse and scams against angry but
legitimate complaint.

They are labelled in the interface as a demo workspace with sample data. They are
replaced, not supplemented, once real Facebook ingestion exists.

## D-020: One Work List With A Recorded Surfacing Reason

The specification defines three lanes: Triage, Review, and Audit. The
implementation is a single ordered work list where every entry records why it
surfaced: `triage`, `institution_sample`, `novel_language`, `uncertainty`, or
`cleared`.

The reason carries the same information with less navigation, and the surfacing
reason is what downstream training needs in order to correct for selection bias.

Random Audit as a separate lane is deferred until real traffic exists to sample.
It is the only route by which false negatives become visible in production, so it
returns before any accuracy claim is made, not before then.

## D-021: Novel Language Is A Distinct Surfacing Reason

Abstention covers low confidence inside what the classifier knows. It does not
cover language the classifier has never seen.

A pattern matcher that finds no rule match cannot distinguish "safe" from
"unfamiliar". Treating both as cleared silently clears exactly the evolving Khmer
slang the product exists to catch.

Comments containing no recognised vocabulary abstain and surface as
`novel_language`. This is the route by which new slang reaches a human and
becomes a labelled seed-dataset candidate.

## D-022: Bearer Tokens Rather Than Session Cookies

The frontend and API are deployed on different sites, `vercel.app` and
`onrender.com`. A `SameSite=None` cookie is blocked by default in Safari and
several other browsers, so a signed-in visitor would appear signed out.

Sessions are bearer tokens held in memory and mirrored to `localStorage`. Only
the SHA-256 of a token is stored server-side, so a database leak does not hand
over usable sessions.

This is a deployment-shaped decision, not a preference. Serving both halves from
one domain would make cookies viable again and is the better long-term answer.

## D-023: Contract First, Then The Half That Carries The Risk

D-016 requires an accepted design handoff and an accepted backend-owned OpenAPI
artifact before implementation, and then states the frontend is implemented
first. The moderation and authentication slices were built backend-first without
recording an exception.

The sequence that holds is: design the screens, define the contract they need,
then implement the half where the risk sits.

- A slice whose risk is the interface implements the frontend first, against
  network interception, exactly as D-016 describes.
- A slice whose risk is authorization implements the backend first, so the
  boundary is proven by tests before any interface depends on it.

The design handoff and the contract still come first in both cases, and live
frontend, API and persistence proof is still required before a slice completes.
Part 1 is authorization-shaped: who is a Platform Administrator, and the rule
that administrators cannot read customer comments.

## D-024: Two Client Page-Connection Methods, One Capability Boundary

The Client Page Connection screen offers **Continue with Facebook** as the
recommended flow and **Connect with Page token** as an advanced flow. Both are
Client capabilities and both converge on the same encrypted, workspace-scoped
Page Connection. Their abilities are determined by the resulting Meta Page
token's tasks and permissions, not by how KCMS obtained it.

The manual flow accepts a Page access token, validates it with the provider, and
derives the Page identity rather than trusting a typed Page name. Stored tokens
never return to the browser. See ADR 0005.

## D-025: Reviewed Account Setup

D-018 is superseded. Public self-signup is disabled. A visitor requests pilot
access, a Platform Administrator reviews the request, and an approved owner
creates credentials through a seven-day, single-use setup link. The sign-in
screen links new visitors to Request access and exposes no Create account path.

This decision governs account creation only. Its earlier demo-only Page
connection exception is superseded by D-026.

## D-026: One KCMS Approval Boundary

Pilot onboarding is the only KCMS approval boundary. After a visitor is
approved, creates credentials, and enters a Client workspace, that Client may
start Meta authorization and connect one of the Pages Meta returns for its own
account. KCMS does not ask the Platform Administrator to approve that Page a
second time.

`workspace.is_sandbox` describes sample-data state; it is not an integration
authorization gate. Meta access, Page selection, and moderation capability are
still constrained by the Client's Meta account, app mode, permissions, Page
tasks, token validation, and KCMS workspace authentication. The former
`/access-requests` Page-approval API and Platform Operations Page-connections
queue are removed.


## D-027: Automatic Removal Waits For A Trained Model

Auto-removal shipped briefly: a HARMFUL verdict deleted the comment from the
Page on arrival, by `system:auto-removal`, with no person involved. It is now
off, behind `auto_removal_enabled`.

A keyword list is not evidence enough to destroy a customer's comment. Deletion
cannot be undone on Facebook, so a false positive from pattern matching is
permanent and silent, and the errors a rule-based matcher makes are exactly the
ones nobody sees. D-010 therefore stands unchanged: every Facebook moderation
Action is a human decision throughout the prototype.

The routing that decides what *would* be auto-removed is kept and tested in both
positions, including the carve-out that never auto-removes institution-directed
criticism. Enabling it when a trained model earns the confidence is one setting,
not rebuilt work.

## D-030: Controlled Facebook Comment Auto-Replies

The team accepts a narrow demo carve-out for rule-based replies on Facebook
comments. A workspace owner explicitly enables the live feature; there is no
separate demo mode. Only newly ingested SAFE comments
matching the first enabled comments rule are eligible. The event log is
idempotent and records provider application only after Meta confirms success.

There is no fallback reply, unsafe-message reply, automatic hide/delete, or
Messenger support. Messenger, worker/webhook ingestion, additional providers,
and broad rollout remain deferred. This decision is recorded in ADR-0006 and
does not weaken D-027's prohibition on automatic removal.
