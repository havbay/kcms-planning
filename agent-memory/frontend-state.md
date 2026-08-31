# Frontend State

**Repository:** `/home/ggwp/dev/KCMS/KCMS-V2/kcms-frontend`
**Remote:** `git@github.com:havbay/kcms-frontend.git`
**Live:** https://kcms-frontend.vercel.app

**Status:** Deployed on Vercel from `main`. The public site, authentication, and
the client dashboard's Overview and Moderate sections are implemented against the
live API. Page connection, Team, Settings, comment review detail, and all
Platform Administrator views have not started.

## Implemented

**Public** - bilingual landing page with sticky header and a mobile navigation
drawer; notice pages for every route that is not yet built, so no path renders
blank.

**Authentication** - sign-up and sign-in with per-field inline validation
(`aria-describedby`, `aria-invalid`, validated on blur); Telegram Login Widget
that only renders when the backend reports the provider is configured; bearer
token session restored on load; route guard; sign-out.

**Client dashboard** - sidebar shell with a sandbox notice; Overview with metrics
derived entirely from live API data; Moderate with the work list, severity and
target, surfacing reason, Leave/Hide/Unhide, and Corrections. Unbuilt sections
appear as plain text marked "Not in the prototype", never as links.

## Accepted Runtime

React 19, TypeScript strict, Vite, Node 22+, npm, Vitest, Testing Library,
Playwright. API types are generated from the backend-owned OpenAPI artifact via
`npm run api:generate`; `schema.d.ts` is never hand-edited. See ADR 0003.

`openapi-typescript` is run through `npx` rather than installed, because it peers
on TypeScript 5 while this project uses 6. The generated types are committed, so
the build never needs the tool.

## Tests And Runtime Evidence

19 Vitest and 14 Playwright tests pass, with strict TypeScript, ESLint, and the
Vite production build. Verified at 1440x1000, 1280x800, 1024x800 and 375x812 in
both languages, with no horizontal overflow and a clean browser console.

Playwright runs six browsers in parallel and fails with
`Object with guid ... was not bound in the connection` when the machine is loaded.
That is resource exhaustion, not a defect; re-run with `--workers=1`.

## Copy Rules

Public claims must stay true as automation grows. "Automatic detection" names the
step and "Pattern matching v0.1" names the engine, so shipping the model changes
one string. Say "Automatic hiding is off today", never "KCMS never acts by
itself". No invented pricing, metrics, testimonials, or accuracy claims.

## Not Yet Implemented

Page connection, Team, Settings, Policy, comment review detail with post and
parent context, full moderation history, metrics with denominators, request
access as a stored record, invitation setup, recovery, and every Platform
Administrator view.

**Next frontend action:** Team. Workspaces and `membership` roles exist in the
backend and nothing reads them yet.
