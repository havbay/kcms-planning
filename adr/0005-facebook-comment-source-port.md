# Facebook Comment Source And Page Credential Boundary

**Status:** accepted  
**Date:** 2026-09-01

## Context

Clients need a simple Page connection while the team also needs a manual path
for controlled prototype testing. Both paths ultimately produce a Facebook Page
access token. Moderation and ingestion must not care how that credential was
obtained, and the browser must never receive a stored token.

## Decision

- The Client workspace offers **Continue with Facebook** as the recommended
  method and **Connect with Page token** as an advanced method.
- Facebook authorization retrieves the Pages available to the authorizing user;
  the Client explicitly chooses and confirms one Page.
- Manual setup accepts a Page access token and derives Page identity from Meta;
  it does not trust a browser-supplied Page name.
- Both methods create the same workspace-scoped `PageConnection` record and
  expose the same KCMS capabilities when Meta grants the same tasks and
  permissions.
- Provider tokens are encrypted at rest, are never returned by the API, and are
  removed on disconnect. The API returns Page identity, method, health,
  permissions/tasks, and synchronization timestamps only.
- A `CommentSource` port separates connection validation, synchronization, and
  provider Actions from moderation domain code. Tests use a controlled adapter;
  production uses the Meta adapter.
- Every hide or unhide remains an explicit authenticated Client request.

## Alternatives considered

- **Facebook authorization only:** simplest for customers, but blocks controlled
  prototype work while Meta configuration or review is incomplete.
- **Manual token only:** quick for developers, but unsuitable as the normal
  experience for nontechnical Clients.
- **Store tokens in browser storage:** rejected because Page tokens are provider
  credentials and browser compromise would expose them.

## Consequences

- Runtime needs Meta application configuration and a separate credential
  encryption key.
- A token with missing tasks can connect only if it passes the capabilities KCMS
  actually requires; connection method never overrides provider authorization.
- OAuth and manual connection converge before ingestion and moderation code.
- Live Meta behavior remains unverified until an authorized Page handshake is
  run and recorded.

## Verification

- Backend tests prove unauthenticated denial, workspace isolation, encrypted
  storage, token non-disclosure, failed validation, and disconnect.
- Adapter tests prove both acquisition methods produce the same public
  connection shape.
- A later controlled Meta check proves Page discovery, comment synchronization,
  and reversible hide/unhide against a Page the owner administers.
