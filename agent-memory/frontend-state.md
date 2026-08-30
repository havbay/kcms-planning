# Frontend State

**Repository:** `/home/ggwp/dev/KCMS/KCMS-V2/kcms-frontend`

**Status:** React/Vite application scaffold and the bilingual landing-page header
and hero are implemented on `feature/landing-header-hero` in the canonical V2
frontend folder. The branch is not pushed or merged.

**Remote:** `git@github.com:havbay/kcms-frontend.git`

**Accepted implementation:** Responsive public header and hero with English and
Khmer switching, compact navigation, Request Access and Sign In targets, and the
disclosed Comment Pathway from pattern matching to human review and decision.

**Accepted runtime:** React 19 with TypeScript strict and Vite on Node 22 or newer,
using npm, Vitest, Testing Library, Playwright, and a generated OpenAPI client. See
`adr/0003-frontend-runtime.md`.

**Accepted design artifacts:** `docs/design/DESIGN.md` and
`docs/design/part-0-landing-header-hero/`. The product owner explicitly selected
direct Codex design under D-017; OpenDesign generation is optional.

**Tests and runtime evidence:** Vitest 5/5 and Playwright 3/3 pass. TypeScript
strict checking, ESLint, Vite production build, and `git diff --check` pass.
Rendered browser checks at 1440x1000 and 375x812 show no horizontal overflow;
the mobile menu and full Khmer switch work, and the browser console is clean.

**Next frontend action:** Product-owner visual review of the implemented header
and hero, then build the next landing-page section only after approval.
