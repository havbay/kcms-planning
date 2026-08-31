# Frontend State

**Repository:** `/home/ggwp/dev/KCMS/KCMS-V2/kcms-frontend`
**Remote:** `git@github.com:havbay/kcms-frontend.git`
**Live:** https://kcms-frontend.vercel.app

**Status:** Deployed on Vercel from `main`. The public site, authentication, and
the complete client dashboard are implemented against the live API. The dashboard
has no placeholder sections left. Platform Administration exists for access
requests only.

## Implemented

**Public** - bilingual landing page with sticky header and a mobile navigation
drawer; notice pages for every route that is not yet built, so no path renders
blank.

**Authentication** - sign-up and sign-in with per-field inline validation
(`aria-describedby`, `aria-invalid`, validated on blur); Telegram Login Widget
that only renders when the backend reports the provider is configured; bearer
token session restored on load; route guard; sign-out.

**Client dashboard** - sidebar shell with a sandbox notice, and five working
sections: Overview, Moderate, Page connection, Team and Settings. Overview reads
a database-computed summary. Moderate is a paginated table with an expandable
detail row. Administration appears in the sidebar only for platform
administrators.

**Application density** - the dashboard uses its own type scale, scoped to
`.dashboard` and `.admin-shell`, so the marketing pages keep their presence. Body
text is 0.8125rem and table rows are roughly 47px, against 1rem and card-sized
rows before. The public site is unchanged.

## Accepted Runtime

React 19, TypeScript strict, Vite, Node 22+, npm, Vitest, Testing Library,
Playwright. API types are generated from the backend-owned OpenAPI artifact via
`npm run api:generate`; `schema.d.ts` is never hand-edited. See ADR 0003.

`openapi-typescript` is run through `npx` rather than installed, because it peers
on TypeScript 5 while this project uses 6. The generated types are committed, so
the build never needs the tool.

## Tests And Runtime Evidence

19 Vitest and 22 Playwright tests pass, with strict TypeScript, ESLint, and the
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

Comment context with post and parent, work list filtering, full moderation
history, Page Policy, metrics with denominators, a workspace switcher, and
Platform Administration beyond access requests.

`/admin/requests` still uses the card layout and no longer matches the density of
the moderation table.

The dashboard is desktop-first while the first market runs its business on a
phone. Raised and deliberately deferred.

**Next frontend action:** comment context, which needs `post_text` and
`parent_text` populated before the screen can be evaluated.
