# Frontend State

**Repository:** `/home/ggwp/dev/KCMS/KCMS-V2/kcms-frontend`
**Remote:** `git@github.com:havbay/kcms-frontend.git`
**Live:** https://kcms-frontend.vercel.app

**Status:** The live Facebook demo path has been exercised with real Page
comments and provider-side hide/unhide. The matching multi-Page contract,
redesigned Overview and moderation UI, and Overview containment repair are now
merged to `main`. PR #6 added Rules/Profile/Page UI, and the sign-in layout fix
at `c607084` is publicly verified.

## Implemented

- Bilingual public landing page, service overview, FAQ, early-access request,
  invitation setup, email/password sign-in, optional Telegram Login Widget,
  route protection, and sign-out.
- Sign-in exposes no public Create account path. New visitors go to Request
  access; approved owners create credentials only through a one-time setup link.
- Client workspace: Overview, Moderate, Page Connection, Team, and Settings.
- Page Connection: **Continue with Facebook** is recommended; an advanced Page
  access-token disclosure is available for assisted setup. Loading, provider
  error, Page choice, connected, capability warning, and disconnect states are
  explicit. A failed start explains whether Meta is unconfigured (`503`) or
  reports a general authorization failure. It never opens a second KCMS
  approval request. The frontend never receives a stored token.
- Moderate: compact server-paginated table with search, review status, severity,
  target, surfacing reason, sort, commenter, linked source post, and deterministic
  pagination. Hide/Unhide appears inline without opening detail; selecting a row
  opens the complete context/verdict panel. Actions and label Corrections remain
  separate controls. Foreground polling supplements the manual sync control.
- Application shell: compact desktop sidebar and a narrow-screen top shell with
  horizontally scrollable navigation. Tables scroll inside their own container;
  comment detail becomes a full-screen mobile panel.
- Platform Administration remains limited to initial pilot-access review; the
  obsolete Page-connections queue is removed.

## Runtime

React 19, strict TypeScript, Vite, Node 22+, npm, Vitest, Testing Library, and
Playwright. API types are generated from the backend-owned OpenAPI artifact with
`npm run api:generate`; generated files are committed and never hand-edited.

## Verification

The current slice passes 74 Vitest tests, strict TypeScript, ESLint with no
errors (one pre-existing Fast Refresh warning), and the production build.

## Copy boundary

"Automatic detection" names the step and "Pattern matching v0.1" names the
current engine. Automatic hiding is off today. Published early-access pricing
now has Starter, Growth, and Enterprise plans; testimonials, accuracy, and
performance claims must still be evidence-backed.

## Not yet implemented

Webhook/background ingestion, full moderation history, workspace switching,
broader Platform Administration, and the trained Khmer model.

**Operational note:** the multi-Page frontend and backend must deploy together.
The frontend calls `/facebook/connections` and per-Page sync/disconnect routes;
deploying it before the matching backend produces a Page Connection 404 and a
generic full-page error.

Dashboard polling owns one client-side sync loop for the authenticated `/app`
shell. It polls connected Pages every 60 seconds while the tab is visible,
skips overlapping requests, and dispatches a refresh event for Moderate. The
Moderate screen keeps its manual sync button but no longer creates a duplicate
timer.

**Vercel note:** `main` is now the Production Branch. Push `c607084` created a
Production deployment automatically; the public alias served the exact CSS and
JavaScript fingerprints from the verified local build.
