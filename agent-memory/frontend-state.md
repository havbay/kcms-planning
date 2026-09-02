# Frontend State

**Repository:** `/home/ggwp/dev/KCMS/KCMS-V2/kcms-frontend`
**Remote:** `git@github.com:havbay/kcms-frontend.git`
**Live:** https://kcms-frontend.vercel.app

**Status:** The live Facebook demo path has been exercised with real Page
comments and provider-side hide/unhide. A teammate's pricing merge introduced
a frontend/backend Page Connection contract mismatch. The matching multi-Page
contract and Overview containment repair are now deployed and publicly verified.

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
  target, surfacing reason, sort, source-post caption/type, and deterministic
  pagination. Selecting a row opens the complete context/verdict panel. Actions
  and label Corrections are deliberately separate controls.
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

The current slice passes 55 Vitest tests, strict TypeScript, ESLint with no
errors (one pre-existing Fast Refresh warning), and the production build.

## Copy boundary

"Automatic detection" names the step and "Pattern matching v0.1" names the
current engine. Automatic hiding is off today. Published early-access pricing
now has Starter, Growth, and Enterprise plans; testimonials, accuracy, and
performance claims must still be evidence-backed.

## Not yet implemented

True multi-Page persistence/API support, webhook ingestion, full moderation
history, workspace switching, broader Platform Administration, and the trained
Khmer model.

**Operational note:** the multi-Page frontend and backend must deploy together.
The frontend calls `/facebook/connections` and per-Page sync/disconnect routes;
deploying it before the matching backend produces a Page Connection 404 and a
generic full-page error.
