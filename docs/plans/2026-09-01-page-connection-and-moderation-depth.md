# Page Connection And Moderation Depth

**Status:** accepted

## Outcome

An approved Client can connect one Facebook Page through Facebook authorization
or an advanced Page-token form. Both methods create the same encrypted Page
connection. The moderation work list becomes a compact, filterable,
server-paginated table with source-post context and a complete review panel.

## Public seams

1. Authenticated Page-connection HTTP endpoints and their generated OpenAPI
   contract.
2. The rendered `/app/connect` connection, loading, failure, and connected
   states.
3. The authenticated comments collection with server-side filters and stable
   pagination.
4. The rendered `/app/moderate` list and comment-detail workflow.

## Tasks

1. Record the Page credential and source-adapter decision.
2. Add backend RED tests for authentication, workspace isolation, token
   non-disclosure, encryption-at-rest, validation failure, and disconnect.
3. Implement the connection repository, Meta adapter seam, routes, migration,
   settings, and deterministic OpenAPI export.
4. Regenerate the frontend contract, add RED component tests, and implement the
   two-method connection interface.
5. Add backend RED tests for comment search/filter combinations, source context,
   and deterministic pagination; then implement them.
6. Regenerate the frontend contract, add RED component tests, and implement the
   compact table, filters, pagination, and review panel.
7. Run backend, frontend, browser, build, and contract-drift gates and update
   canonical state files with only produced evidence.

## External boundary

Tests replace Meta HTTP with a controlled adapter. No live Facebook request is
made until the product owner supplies an authorized Page and explicitly starts
the integration check.
