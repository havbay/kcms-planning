# Frontend State

**Repository:** `/home/ggwp/dev/KCMS/KCMS-V2/kcms-frontend`

**Status:** The complete public landing page is implemented on
`feature/landing-header-hero` in the canonical V2 frontend folder: header/hero,
"How KCMS works", "Built for Khmer", "Human control", "Early access", and the
site footer. The branch is committed locally but not pushed or merged.

**Remote:** `git@github.com:havbay/kcms-frontend.git`

**Accepted implementation:** Fully bilingual responsive landing page. Header
and hero with Khmer/English switching, compact navigation, Request Access and
Sign In targets, and the disclosed Comment Pathway. "How KCMS works" explains
Page connection, pattern-matched prioritization, and human-controlled decisions.
"Built for Khmer" contrasts an institution-directed complaint that stays visible
against person-directed abuse that is surfaced for review. "Human control"
states five guarantees, including that KCMS does not train itself from client
moderation actions, plus the current Facebook-only scope. "Early access" offers a
single Pilot access card with no invented price tiers, deliberately placed after
both trust sections. The footer carries link columns and the prototype-status
disclosure.

**Accepted runtime:** React 19 with TypeScript strict and Vite on Node 22 or newer,
using npm, Vitest, Testing Library, Playwright, and a generated OpenAPI client. See
`adr/0003-frontend-runtime.md`.

**Accepted design artifacts:** `docs/design/DESIGN.md` and
`docs/design/part-0-landing-header-hero/`. The product owner explicitly selected
direct Codex design under D-017; OpenDesign generation is optional.

**Tests and runtime evidence:** Vitest 15/15 and Playwright 6/6 pass.
TypeScript strict checking, ESLint, Vite production build, and `git diff --check`
pass. A unit test asserts the section order is hero, how-it-works,
khmer-context, human-control, early-access, so the pilot ask cannot drift above
the trust sections. Rendered checks at 1440x1000, 1024x800, and 375x812 show no
horizontal overflow in either language; the Khmer heading is flush with its
container with no clipping, and the header navigation fits at 1024px.

**Removed by decision:** The "Prototype rule" callout was deleted from the
workflow section as redundant; the same message already appears in the hero, the
pathway caption, step 03, and the footer status line. Its unique
no-self-training claim was preserved in "Human control". The FAQ section was cut
for the same reason, with its one unanswered question (Messenger and Instagram)
folded into the "Human control" scope line. No separate final CTA band was added,
because the Early access card already closes the page with its own call to
action.

**Open items:** The footer links `/contact` and `/privacy`, and the header links
`/request-access` and `/sign-in`; none of these routes exist yet.

**Next frontend action:** Product-owner review of the complete landing page, then
Vercel preview deployment for team review.
