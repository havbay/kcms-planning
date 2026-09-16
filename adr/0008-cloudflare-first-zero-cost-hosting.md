# Cloudflare-first zero-cost hosting

**Status:** Accepted for implementation on 2026-09-16

## Context

The Render production database expires on 2026-09-29, Render permits one free
PostgreSQL instance per workspace, and the owner does not approve recurring
hosting spend yet. Cloudflare Python Workers now support FastAPI and `asyncpg`,
and Hyperdrive can connect Workers to an external PostgreSQL database.

The owner asked to prepare the production target first and to move the existing
Render production settings into Cloudflare. This supersedes ADR-0007's Render
backend target and deployment ordering. It does not authorize changing the live
API DNS record before the Cloudflare deployment passes direct-URL verification.

## Decision

- Keep the production and staging frontends in separate Cloudflare Pages
  projects, using `main` and `staging` respectively.
- Run the production and staging FastAPI APIs as separate Cloudflare Python
  Workers. Prepare `findmoy-api-production` first and verify its `workers.dev`
  URL before attaching `api.findmoy.app`.
- Use separate external PostgreSQL databases for production and staging. Neon
  Free is the current zero-cost target. Cloudflare Hyperdrive provides one
  isolated connection binding per environment.
- Keep PostgreSQL as the database contract. Do not migrate KCMS to D1 because
  its SQLite semantics require a separate schema and repository rewrite.
- Store sensitive configuration as Cloudflare Worker secrets. Render's MCP does
  not return secret values, so use Render's authenticated environment export,
  transfer values directly to Cloudflare, and delete the local export after a
  successful upload.
- Do not run migrations inside Worker request startup. Apply forward-only
  migrations as a separate reviewed deployment step.
- Replace the process-owned quarantine loop with a one-minute Cloudflare Cron
  Trigger. The Render runtime retains its existing process loop while it remains
  active as rollback.
- Keep Render and Vercel available until the Cloudflare production and staging
  verification checklists pass.

## Free-plan constraints

- Workers Free permits 100,000 requests per day and 10 ms CPU per invocation.
- Hyperdrive Free permits 100,000 database statements per day.
- Python Workers and Python Hyperdrive support are beta features.
- If representative production requests exceed 10 ms CPU, stop the cutover and
  present the Workers Paid cost before enabling a paid plan.

## Rollback

Leave `kcms-backend.onrender.com`, the existing Render environment, and the
Vercel frontend intact. Until DNS cutover, rollback requires no action. After
cutover, restore the API DNS target to the Render service and restore the
frontend target to the retained Vercel deployment if necessary.

## Identity provider decisions — 2026-09-16

Production and staging previously shared one Clerk **development** instance:
the live Vercel bundle and `.env.staging` both carried
`pk_test_Y2VudHJhbC1jYXQtOTQ3Mi5jbGVyay5hY2NvdW50cy5kZXYk`. That put staging
sign-ins and production sign-ins in the same user pool.

- Production now uses the Clerk **production** instance on `clerk.findmoy.app`
  (`pk_live_Y2xlcmsuZmluZG1veS5hcHAk`, issuer `https://clerk.findmoy.app`).
  Its four CNAMEs (`clerk`, `clkmail`, `clk._domainkey`, `clk2._domainkey`) are
  DNS-only records in Cloudflare and are verified.
- Staging keeps the Clerk development instance
  (`central-cat-9472.clerk.accounts.dev`). The pools are now separate.
- Existing development-instance users do not carry over to the production pool.
- Both instances are in Clerk access mode **Open**, so public sign-up is on.

`PUBLIC_SIGNUP_ENABLED` is **not** the public-access switch and must stay
`false` in both environments. It exposes a hidden legacy email/password
endpoint, marks every workspace it creates `is_sandbox = true`, and seeds that
workspace with scripted fixture comments.

## Meta application — owner override, 2026-09-16

The owner chose to point staging at the **production** Meta application rather
than a separate test app, after being shown the risk. Recorded consequences:

- Staging holds production Meta credentials, contrary to the separation rule in
  ADR-0007.
- A staging defect can act on real connected Pages: hide, delete, or reply to
  genuine customer comments.
- Staging's Neon database is empty, so no Page is connected there yet. The
  exposure begins the moment someone connects a real Page from staging.

Mitigation until a test app exists: do not connect a production Page from
staging, and keep `AUTO_REMOVAL_ENABLED` and auto-reply rules off in staging.
