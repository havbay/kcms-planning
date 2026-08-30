# Vertical Slice Checklist

Copy this file to `docs/superpowers/plans/YYYY-MM-DD-part-N-name.md` before
starting a new part. Replace the title and concrete paths during planning; do not
begin implementation until every required path and contract is named.

## Slice Identity

- Part number and name:
- User and capability:
- Start state:
- Successful end state:
- Approved design artifact paths:
- OpenAPI operation IDs:
- Database migrations:
- Frontend routes:
- Runtime evidence command:

## Gates

- [ ] Product requirements mapped to acceptance tests.
- [ ] Desktop, mobile, English, Khmer, and API-state designs approved.
- [ ] Backend-owned OpenAPI change accepted and frontend client regenerated.
- [ ] Frontend behavior test fails for the intended missing behavior.
- [ ] Frontend implementation passes component tests through approved
  network-boundary simulation.
- [ ] Backend contract test fails for the intended missing behavior.
- [ ] Backend implementation passes allowed and denied tests.
- [ ] Backend OpenAPI export reviewed and frontend client regeneration is clean.
- [ ] Playwright completes the real cross-repository journey.
- [ ] Accessibility, console, overflow, and responsive checks pass.
- [ ] Memory and decision records updated with evidence.
- [ ] Frontend and backend commits are independently reviewable.

## Evidence Vocabulary

- `source-confirmed`: code or configuration supports the claim.
- `test-confirmed`: an automated test passed the claim.
- `runtime-confirmed`: a running service produced authoritative proof.
- `unverified`: the claim remains an assumption or future requirement.
