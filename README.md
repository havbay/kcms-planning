# KCMS V2 Planning And Agent Memory

This repository is the canonical product, architecture, and delivery memory for
the KCMS V2 rebuild. The frontend and backend are independent code repositories;
this repository prevents their product rules, API assumptions, and delivery
status from drifting apart.

## Local Layout

```text
KCMS-V2/
├── kcms-planning/   # this repository
├── kcms-frontend/   # Client and Platform Administrator web application
└── kcms-backend/    # API, workers, integrations, and persistence
```

## Repositories And Live Environments

| Repository | Live | Hosting |
|---|---|---|
| [`kcms-frontend`](https://github.com/havbay/kcms-frontend) | https://kcms-frontend.vercel.app | Vercel |
| [`kcms-backend`](https://github.com/havbay/kcms-backend) | https://kcms-backend.onrender.com | Render, Singapore |
| [`kcms-planning`](https://github.com/havbay/kcms-planning) | — | — |

Vercel auto-deploys the frontend from `main`. Render's repository webhook is
currently unreliable, so backend deploy completion must be checked explicitly.

## Current Delivery Boundary

The deployed product contains the public site, authentication, isolated client
workspace, moderation and request administration. An expanded landing page and
the complete public pilot-onboarding journey are implemented in local feature
branches pending owner review:

```
public request ──▶ administrator decision ──▶ optional SMTP
                                                    └─▶ manual setup link
                                                             │
                                                             ▼
                                               client chooses password
```

Email delivery is optional by design; the approval journey remains functional
and audited before a provider is configured.

## Reading Order

1. `00-product-specification.md`
2. `04-implementation-roadmap.md`
3. `DESIGN.md` for design and frontend work
4. `agent-memory/current-state.md`
5. The relevant frontend or backend plan
6. `03-api-contract.md`
7. Applicable records under `adr/`

## Working Rule

Only one implementation part may be active at a time. A part is complete only
when its approved user journey works through the real frontend, API, persistence,
and authorization boundaries and its automated and manual gates pass.

No credentials, tokens, cookies, private customer data, or real Facebook comment
content may be written to this repository.
