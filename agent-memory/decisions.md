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

## D-005: OpenDesign Before Frontend Implementation

Every frontend slice receives an approved OpenDesign handoff before production UI
implementation begins. OpenDesign output does not define backend behavior.

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

Each product slice begins with an accepted OpenDesign handoff and an accepted
backend-owned OpenAPI artifact. The frontend behavior is implemented first. API
responses may be simulated only by network interception in tests and an explicitly
enabled local preview; production entry points and feature modules never import
fixtures or fake customer data. The matching backend follows, and live frontend,
API, and persistence proof is still required before the slice completes.
