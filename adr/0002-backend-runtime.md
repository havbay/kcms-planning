# FastAPI Modular Monolith Runtime

**Status:** accepted

**Date:** 2026-08-30

## Context

KCMS V2 needs a greenfield backend for a focused Facebook Page comment-moderation
MVP. It must preserve transactional moderation history, workspace isolation, a
replaceable classifier, background integration work, deterministic HTTP contracts,
and a later path to Python-based Khmer NLP without making model development block
the functional prototype.

The frontend is implemented first against an accepted OpenAPI artifact and
network-boundary simulation. The backend must then implement that contract and
replace simulation with live database-aware behavior before a part is complete.

## Decision

KCMS V2 uses a FastAPI modular monolith on Python 3.12 or newer.

- uv owns Python installation selection, dependency resolution, the lock file, and
  all project commands.
- FastAPI owns HTTP routing and OpenAPI 3.1 generation.
- Pydantic v2 models define validated settings and transport schemas.
- asyncpg is the PostgreSQL driver. Database access is asynchronous and explicit;
  application services own transaction boundaries.
- PostgreSQL is the only production database. Docker Compose provides the local
  Part 0 database without committed credentials.
- Alembic owns forward-only schema migrations when persistent domain tables begin.
- One deployable contains the API, modular application/domain code, and background
  workers. Modules communicate through Python interfaces and transactions rather
  than network calls.
- The backend exports an OpenAPI JSON artifact from the application factory using
  stable key ordering and a trailing newline. A test regenerates the artifact and
  compares bytes so contract drift fails deterministically.
- GET /api/v1/health executes SELECT 1 through an asyncpg pool. It returns a typed
  healthy response only when PostgreSQL responds and a typed 503 degraded response
  otherwise.

The package boundary is src/kcms. HTTP composition lives under api, database
ownership under shared/database, and later product capabilities remain focused
modules in the same deployable.

## Alternatives Considered

### NestJS modular monolith

NestJS offers strong TypeScript alignment with the frontend, dependency injection,
mature HTTP tooling, and good worker support. It would reduce language switching
for web engineers. It was not selected because classifier/evaluation work is
Python-oriented, Python integration would eventually add a second service or
bridge, and TypeScript types still would not replace an executable OpenAPI
boundary between independent repositories.

### Django with Django REST Framework

Django provides batteries-included authentication, ORM, migrations, and
administration. It is attractive for conventional account-heavy products. It was
not selected because KCMS requires an async integration and classification
pipeline, an intentionally small API foundation, and deterministic contract
ownership without adopting Django's larger framework surface at Part 0.

### Multiple backend services

Separate identity, ingestion, moderation, and model services could scale
independently. They were rejected for the MVP because distributed transactions,
deployment coordination, and contract proliferation would increase operational
risk before real load justifies those costs. Module ports preserve a later
extraction path.

### SQLAlchemy instead of direct asyncpg

SQLAlchemy offers a mature unit-of-work and mapping layer. Direct asyncpg is
selected for the walking skeleton because the database boundary is small and
explicit, with less abstraction between the health probe and PostgreSQL. The team
may accept a later ADR for SQLAlchemy if domain persistence complexity warrants
it; that change cannot weaken application-owned transaction invariants.

## Consequences

- Backend and later NLP work share Python tooling and data types, while the
  frontend remains isolated through OpenAPI.
- The team must enforce module boundaries through structure and tests rather than
  deployment boundaries.
- Direct asyncpg requires deliberate query organization, mapping, and transaction
  handling as persistence grows.
- uv.lock and the Python version floor make developer and CI environments
  reproducible.
- OpenAPI changes are reviewed before frontend client regeneration.
- A passing unit test with a fake database probe is insufficient; Part 0 needs a
  live PostgreSQL health result.

## Verification

This ADR records an accepted planning decision. No FastAPI application, PostgreSQL
connection, OpenAPI export, migration, or runtime command was executed by this
planning-only change. Those claims remain unverified until the exact Part 0 plan
produces test-confirmed and runtime-confirmed evidence.
