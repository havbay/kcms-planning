# Frontend State

**Repository:** `/home/ggwp/dev/KCMS/KCMS-V2/kcms-frontend`
**Remote:** `git@github.com:havbay/kcms-frontend.git`
**Live:** https://kcms-frontend.vercel.app

**Status:** The approved production baseline is live from `main`. Expanded
onboarding, Page Connection, and moderation-depth work is local on
`feature/landing-header-hero` and must not be described as live until reviewed,
pushed, deployed, and checked against the deployed backend.

## Implemented

- Bilingual public landing page, service overview, FAQ, early-access request,
  invitation setup, email/password sign-in, optional Telegram Login Widget,
  route protection, and sign-out.
- Client workspace: Overview, Moderate, Page Connection, Team, and Settings.
- Page Connection: **Continue with Facebook** is recommended; an advanced Page
  access-token disclosure is available for assisted setup. Loading, provider
  error, Page choice, connected, capability warning, and disconnect states are
  explicit. The frontend never receives a stored token.
- Moderate: compact server-paginated table with search, review status, severity,
  target, surfacing reason, sort, source-post caption/type, and deterministic
  pagination. Selecting a row opens the complete context/verdict panel. Actions
  and label Corrections are deliberately separate controls.
- Application shell: compact desktop sidebar and a narrow-screen top shell with
  horizontally scrollable navigation. Tables scroll inside their own container;
  comment detail becomes a full-screen mobile panel.
- Platform Administration remains limited to request review.

## Runtime

React 19, strict TypeScript, Vite, Node 22+, npm, Vitest, Testing Library, and
Playwright. API types are generated from the backend-owned OpenAPI artifact with
`npm run api:generate`; generated files are committed and never hand-edited.

## Verification

The current local slice passes 31 Vitest tests, strict TypeScript, ESLint with no
errors, the Vite production build, and 24 Playwright tests with one intentional
skip. Browser inspection covered desktop and phone widths in the real local app:
no page-level horizontal overflow, 10 moderation rows per page, an internal
table scrollbar, and a full-width mobile comment panel.

## Copy boundary

"Automatic detection" names the step and "Pattern matching v0.1" names the
current engine. Automatic hiding is off today. No invented pricing, metrics,
testimonials, or accuracy claims are permitted.

## Not yet implemented

Live Meta verification, comment synchronization, provider-side hide/unhide,
full moderation history, workspace switching, broader Platform Administration,
and the trained Khmer model.

**Next frontend action:** only after the live Facebook source contract works,
replace seeded context with real synchronized posts/comments and verify the same
review UI against a controlled Page.
