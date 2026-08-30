# KCMS V2 Backend Redesign Plan

## Responsibility

The backend owns domain invariants, authentication, authorization, persistence,
integration credentials, ingestion, classification orchestration, routing,
moderation records, annotation records, metrics, and auditability.

## Part 0 Technology Decision

The team will compare at least FastAPI and NestJS before scaffolding. The decision
must be recorded in `adr/0002-backend-runtime.md` using these criteria:

- Team implementation and debugging experience.
- Type safety across HTTP and persistence boundaries.
- Background-job and Facebook webhook support.
- Migration and transactional integrity.
- Testing speed and tooling maturity.
- Model-serving integration with Python-based NLP tooling.
- Deployment complexity and operating cost.
- Ability to preserve the domain invariants in the product specification.

FastAPI is the default recommendation because the classifier and evaluation work
are Python-oriented. The team may choose NestJS only when the ADR demonstrates a
clear operational or staffing advantage.

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
├── annotation/      # assignments, labels, skips, disagreements, export
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
- Automatic actions require an accepted versioned Page Policy.
- Events that trigger email or background work use a transactional outbox.
- Metrics never represent an absent denominator as zero.

## Quality Gates

- Formatting, linting, static typing, unit, integration, and migration tests pass.
- OpenAPI generation is deterministic and reviewed as a contract change.
- Authorization tests prove both allowed and denied directions.
- Cross-workspace identifier tests prove tenant isolation.
- Ingestion idempotency and retry tests pass.
- Decision-history invariants pass under transaction rollback and retries.
- A live smoke test verifies health, authentication, the active slice, and database
  persistence through public HTTP routes.
