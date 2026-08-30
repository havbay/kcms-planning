# KCMS V2 Agent Instructions

These instructions apply to every file in this planning repository and to work
coordinated across the sibling `kcms-frontend` and `kcms-backend` repositories.

## Start Of Every Task

Read, in order:

1. `00-product-specification.md`
2. `04-implementation-roadmap.md`
3. `agent-memory/current-state.md`
4. `agent-memory/next-actions.md`
5. The relevant frontend or backend plan
6. `03-api-contract.md` when the task crosses the HTTP boundary
7. Applicable accepted records in `adr/`

Inspect the actual Git status of all affected repositories before editing. Treat
unrecognized changes as team work and do not revert them.

## Delivery Discipline

- Work on only the active part recorded in `current-state.md`.
- Build vertical slices. Do not complete an API without its usable frontend, or a
  frontend workflow without its persisted API behavior.
- Write the failing test before implementing behavior.
- Keep production code free of demo routes and fabricated customer data.
- Preserve KCMS domain invariants from the product specification.
- Use OpenAPI as the executable frontend/backend contract.
- Record architecture changes as ADRs before depending on them.
- Do not mark a part complete from source inspection alone. Record runtime proof.
- Commit small, independently reviewable changes in the repository they affect.

## End Of Every Task

Update the applicable files under `agent-memory/`:

- `current-state.md`: active part, last verified state, blockers, and evidence.
- `frontend-state.md` or `backend-state.md`: repository-specific implementation.
- `integration-state.md`: contract version and live cross-repository checks.
- `decisions.md`: only decisions the team has accepted.
- `completed-slices.md`: only after every definition-of-done gate passes.
- `next-actions.md`: ordered, concrete work that can be started immediately.

Every evidence entry must say whether it is source-confirmed, test-confirmed,
runtime-confirmed, or unverified.

## Security

Never store passwords, API keys, session tokens, Facebook access tokens, private
customer identifiers, or authenticated captures. Use synthetic test fixtures and
redacted evidence only.
