# KCMS V2 Frontend Redesign Plan

## Responsibility

The frontend owns user experience, presentation state, accessibility,
localization, and consumption of the versioned backend API. It does not own
authorization, policy decisions, classification, or persistence.

## Design Workflow

1. Install and verify OpenDesign and its Codex integration.
2. Create one KCMS V2 project and one canonical `DESIGN.md`.
3. Design each active vertical slice before implementation.
4. Approve desktop, tablet, mobile, English, Khmer, loading, empty, error, denied,
   stale, partial, and success variants required by that slice.
5. Export approved reference artifacts into `docs/design/<part>/` in the frontend
   repository.
6. Implement reusable components against the approved references.
7. Compare Playwright screenshots and references in the same frame.

## Information Architecture

- Public: landing, request access, sign in, invitation setup, and recovery.
- Moderator: Triage, Review, Audit, history, Actions, and Corrections.
- Administrator: moderator capabilities plus Overview, Team, Policy, and Metrics.
- Annotator: Label Workspace, skips, progress, and Disagreements.
- Operator: access requests, workspace onboarding, fleet health, and support audit.

Navigation is capability-based. A role label alone does not decide the landing
screen when a user has moderation capability.

## Proposed Frontend Boundaries

```text
src/
├── app/             # routing, providers, shell, and error boundary
├── api/             # generated client and request adapters
├── components/      # shared accessible UI primitives
├── design/          # tokens and theme implementation
├── features/        # one folder per product capability
├── localization/    # English and Khmer resources
└── test/            # fixtures and test utilities only
```

Runtime sample data is prohibited. Fixtures stay under test ownership and are not
imported by production entry points.

## API State Requirements

Every API-backed region implements:

- Loading with stable dimensions.
- Empty with an accurate explanation and available next action.
- Permission denied without leaking private data.
- Backend error with retry and retained user input.
- Stale data with last-success time and refresh.
- Partial data with successful regions preserved.
- Success feedback announced near the initiating action.
- Session expiry with a safe return to sign in.

## Quality Gates

- TypeScript strict mode passes.
- Unit and component tests pass.
- Generated API client matches the accepted OpenAPI document.
- Playwright proves the active slice against the running backend.
- No console errors or unintended horizontal overflow.
- Keyboard order, focus visibility, labels, and dialogs are correct.
- English and Khmer layouts pass at 375, 768, 1024, and 1440 CSS pixels.
- Representative screens remain usable at 200 percent browser zoom.
- Production build completes within the agreed bundle budget recorded in Part 0.
