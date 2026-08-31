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

**Update `agent-memory/` before reporting back, in the same task that made the
change.** Not "eventually", not "next session". A state file that describes a
system which no longer exists is worse than no state file, because the next
person trusts it.

This has already gone wrong once: `backend-state.md` still said "no application
scaffold" while sixty-eight tests ran against a deployed API, and
`next-actions.md` still said "do not begin backend runtime implementation early".

### What triggers an update

| You did this | Update |
|---|---|
| Added or changed an endpoint | `backend-state.md`, `integration-state.md` |
| Added or changed a screen | `frontend-state.md` |
| Finished a slice end to end | `completed-slices.md`, `current-state.md` |
| Made a decision, or deviated from one | `decisions.md` |
| Changed what should happen next | `next-actions.md` |
| Deployed, or changed environment config | `backend-state.md` |
| Lost time to something non-obvious | Wherever it will be looked for |

### Write down the things that cost time

Features are visible in the code. These are not, and they cost hours to
rediscover:

- Deploy and environment behaviour that surprised you
- Tooling versions that conflict, and the workaround chosen
- Test infrastructure that fails for environmental rather than code reasons
- Anything where the obvious diagnosis was wrong

### Delete what is no longer true

Removing a stale claim matters as much as adding a new one. Before finishing,
search the memory files for statements the task just invalidated.

### Evidence labels

Every evidence entry says whether it is source-confirmed, test-confirmed,
runtime-confirmed, or unverified. Do not label something runtime-confirmed
because a test passed; that is test-confirmed.

## Security

Never store passwords, API keys, session tokens, Facebook access tokens, private
customer identifiers, or authenticated captures. Use synthetic test fixtures and
redacted evidence only.
