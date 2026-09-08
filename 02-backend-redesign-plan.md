# KCMS V2 Backend Redesign Plan

## Responsibility

The backend owns domain invariants, authentication, authorization, persistence,
integration credentials, ingestion, classification orchestration, routing,
moderation records, annotation records, metrics, and auditability.

## Accepted Part 0 Technology Decision

`adr/0002-backend-runtime.md` accepts a FastAPI modular monolith on Python 3.12 or
newer, managed with uv. The decision used these criteria:

- Team implementation and debugging experience.
- Type safety across HTTP and persistence boundaries.
- Background-job and Facebook webhook support.
- Migration and transactional integrity.
- Testing speed and tooling maturity.
- Model-serving integration with Python-based NLP tooling.
- Deployment complexity and operating cost.
- Ability to preserve the domain invariants in the product specification.

NestJS remains a documented alternative, not an open Part 0 decision.

## Architecture

Use a modular monolith for the MVP. Modules communicate through application
interfaces and transactions, not network calls. Background workers remain in the
backend repository and reuse domain/application code.

```text
src/kcms/
├── api/             # HTTP transport and OpenAPI composition
├── identity/        # accounts, invitations, sessions, and recovery
├── access/          # roles, capabilities, and Page authorization
├── customers/       # requests, workspaces, and Pages
├── integrations/    # Facebook and scripted source adapters
├── classification/  # classifier port and Verdict persistence
├── moderation/      # routing, Work List, Actions, Corrections, history
├── model_development/ # later internal labels, datasets, evaluation
├── policy/          # versioned Page Policy
├── metrics/         # evaluation and operational measures
├── operations/      # fleet health and audited support tools
└── shared/          # database, clock, identifiers, and events
```

## Security Baseline

- Passwords use a modern memory-hard password hash.
- Browser sessions use random server-side session records and Secure, HttpOnly
  cookies in production.
- State-changing cookie-authenticated requests have CSRF protection.
- Session revocation, logout-all, recovery, and invitation expiry are supported.
- Authorization is enforced in backend application services, not only route UI.
- Customer workspace boundaries are tested for every resource identifier.
- Integration secrets are encrypted at rest and never returned to the frontend.
- Sensitive actions are rate-limited, attributable, and written to audit history.

## Data And Integration Rules

- Database migrations are forward-only and tested from an empty database.
- Ingestion is idempotent by external comment identity and edit revision.
- The comment-source port supports scripted local fixtures and Facebook without
  changing moderation code.
- Classifier adapters return the same versioned two-axis Verdict contract.
- Every initial-MVP Facebook moderation Action requires an authenticated human
  request. The separate, narrow Facebook comment-reply carve-out is governed by
  ADR-0006 and does not include hide, unhide, leave, or delete.
- Events that trigger email or background work use a transactional outbox.
- Metrics never represent an absent denominator as zero.

Internal manual dataset creation and trained-model work occur only after the
functional prototype path is usable. They remain separate from the client UI and
must not block the initial MVP. Messenger automation and additional providers
are outside the initial backend scope; controlled Facebook comment replies are
the ADR-0006 demo exception.

## Quality Gates

- Formatting, linting, static typing, unit, integration, and migration tests pass.
- OpenAPI generation is deterministic and reviewed as a contract change.
- Authorization tests prove both allowed and denied directions.
- Cross-workspace identifier tests prove tenant isolation.
- Ingestion idempotency and retry tests pass.
- Decision-history invariants pass under transaction rollback and retries.
- A live smoke test verifies health, authentication, the active slice, and database
  persistence through public HTTP routes.
