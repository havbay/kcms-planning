# Backend State

**Repository:** `kcms-backend` (canonical sibling application repository)

**Status:** No application scaffold. The root agent workflow is committed on
`main` at `85dbfdf`.

**Remote:** Read-only verified as canonical at `havbay/kcms-backend`; the legacy
URL redirects to it. No push was performed in this planning-only task.

**Naming decision:** `kcms-backend` is accepted; no trailing hyphen belongs to the
application repository name.

**Accepted runtime:** FastAPI modular monolith on Python 3.12 or newer with uv,
Pydantic, asyncpg/PostgreSQL, and deterministic OpenAPI generation. See
`adr/0002-backend-runtime.md`.

**Accepted implementation:** None.

**Tests and runtime evidence:** None.

**Next backend action:** Wait for the accepted frontend-first implementation plan
and usable frontend foundation before starting backend RED/GREEN work. Claim no
runtime evidence until its commands actually pass.
