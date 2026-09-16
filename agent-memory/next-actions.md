# Next Actions

Ordered by dependency and value. No dates.

## Stabilize Facebook operations

1. Add webhook or worker-backed synchronization. Current polling runs in the
   authenticated dashboard every 60 seconds and is not a server-side scheduler.
2. Add explicit credential expiry, Page-level retry, and reconciliation states.

## Automated Replies

3. Run the controlled real-Page comment-reply demo under ADR-0006: one owner,
   one connected Page, one enabled comments rule, and a unique safe test comment.
4. Add first-match overlap warnings and an admin-facing event detail view.
5. Add retry/reconciliation handling for provider failures, then measure replies
   sent on comments later labelled as complaints; target zero.

## Complete moderation operations

6. Add full append-only moderation history and provider reconciliation.
7. Add failure/retry states for provider rate limits and
   unavailable webhooks.
8. Add Random Audit only when real traffic exists to sample.

## Product operations

9. Configure transactional SMTP when a verified sender domain is available;
   the audited manual setup-link fallback remains valid until then.
10. Extend the local Platform Administration slice with user lifecycle,
   integration audit logs, and safe operational audit views without exposing
   ordinary customer comment content; then deploy the backend and frontend
   together after review.
11. Add workspace switching before one user manages multiple organizations.

## Model track

12. Write the Khmer annotation guideline and collect authorized manual seed data.
13. Keep training and evaluation conversations separated, train offline, and
    deploy only a version that passes false-suppression and missed-harm gates.
14. Corrections feed a reviewed future training round; the live system never
    retrains automatically.

## Deliberately deferred

Telegram alerts, Messenger/Instagram, suggested replies, buying-intent labels,
and controlled automatic hiding. Generic sales-performance tracking remains out
of scope.


## Deployment reality

Render has required manual deployment. Vercel now auto-deploys pushes to `main`;
the `c607084` production deployment proved the corrected branch setting. Earlier
Vercel pushes were previews because its Production Branch was stale. Render
deploys in the service history are triggered `api` or `manual`, never `commit`,
despite `autoDeploy:
yes`.

Deployment procedure:
- backend: Render MCP `trigger_deploy` on `srv-daa8uepf2nfc739j4eb0`
- frontend: push `main`, then verify the automatic Production deployment; use
  `npx vercel --prod --yes` only as a fallback

Verify by fingerprint, not by timestamp: compare the live bundle name against
`dist/assets/index-*.js`, and the live `/openapi.json` against the committed
artifact. A rolling deploy will otherwise answer from the old instance.

## Required for the Page demo

`META_GRAPH_VERSION` and `INTEGRATION_ENCRYPTION_KEY` must be set on Render or
connecting a Page returns 503.

## Hide verification after provider-result fix

After deployment, hide one newly imported visitor comment and confirm the KCMS
history says `provider_applied=true`. Verify visibility from a logged-out or
second non-author/non-Page-admin Facebook viewer; the commenter, their friends,
and Page managers may still see a hidden comment. If KCMS reports provider
success but that independent viewer still sees it, inspect the exact comment id
and Meta response before changing the action model.


## Authentication redesign — drafted, not approved

`docs/plans/2026-09-02-authentication-and-access-redesign.md` records an agreed
direction: owners sign up with Facebook, Google, or email and password; owners
create team member accounts inside the product; one sign-in screen for everyone.

Do not implement it yet — the team is finishing other work first.

Do not build `POST /api/v1/team/invitations/{token}/registration`. The draft
supersedes it: owner-created accounts mean an invited person never registers.

Facebook-only signup cannot ship while the Meta app is in Development mode,
where Facebook Login admits only people holding a role on the app.


## Cleared sampling — drafted, not approved

`docs/plans/2026-09-03-cleared-sampling.md`. Cleared comments stay out of the
queue by the team's decision. A small random sample is surfaced instead, as a
new `cleared_sample` surfaced_reason, so false negatives can be found and
corrections can be recorded on comments the matcher passed over.

Sample rate is unresolved and should be chosen against the connected Page's real
volume, not guessed.

Separately: `surfaced_reason=cleared` is offered as a filter in the API and the
UI but the work list excludes cleared rows before filtering, so it always
returns nothing. Remove the option.


## Auto-removal is off

`auto_removal_enabled` defaults false. Focus is the rule-based path: the
per-workspace keywords Rin added, and the cleared-sampling draft.

The landing page's "humans decide every moderation action" is true again, so the
copy rewrite that was pending is no longer needed.


## Moderation actions — drafted, not approved

`docs/plans/2026-09-03-moderation-actions.md`. Restores HIDE and UNHIDE
alongside LEAVE and DELETE, so a moderator has a reversible option for a comment
they are unsure about.

`set_comment_hidden` still exists on the Graph client and the action table still
permits both kinds, so the work is mostly widening ActionKind and the buttons.

Verified constraint: a Page cannot hide its own comments — Meta reports
can_hide false and refuses with "(#200) Can not hide or unhide this comment".
It will delete the same comment. Decide whether can_hide is stored at ingest
before building the buttons.

Safe comments stay out of the dashboard. That is settled, not a defect.

## Cloudflare-first hosting

Completed: separate Neon production/staging databases, separate Hyperdrive
bindings, production and staging Workers, verified production data restore,
empty staging schema, staging API custom domain, and the staging Pages
deployment. The staging frontend and backend pass HTTPS, health, database, API
URL, and CORS checks. Render and Vercel remain available for rollback.

Verified 2026-09-16:
- Neon `findmoy-production`: 21 tables, 23 migrations, 26 users, 19 workspaces,
  109 comments, 4 page connections, 109 verdicts, 57 actions.
- Neon `findmoy-staging`: 21 tables, 23 migrations, 0 rows in every data table.
- Render MCP cannot open a Postgres connection (`SSL/TLS required`), so the
  restore has not been re-compared against the Render source in this session.
  Do not delete `kcms-postgres` until that comparison is done another way.
- The Render database still carries the temporary firewall rule
  `110.235.254.164/32 "Temporary KCMS migration"`. It was never removed. Remove
  it.
- Clerk production instance deployed on `clerk.findmoy.app` and verified.
  `CLERK_JWT_ISSUER` on the production Worker is now
  `https://clerk.findmoy.app`.
- `main` is a strict ancestor of `staging` in both repositories, so the
  production release is a fast-forward.

Blocking defect found and fixed on 2026-09-16 — Clerk sign-in on Workers:

`jwt.PyJWKClient` fetches the Clerk key set with blocking `urllib.request`.
Cloudflare Python Workers cannot do synchronous socket I/O, so the fetch raised
`PyJWKClientConnectionError`, a `PyJWTError` subclass that
`_verify_clerk_token` swallowed and reported as a plain 401. Every
authenticated request failed on **both** Workers, and the frontend retried the
exchange two to three times a second indefinitely. The staging spinner
"កំពុងទាញមតិយោបល់…" never resolved and sign-out could not complete.

The earlier verification passed only `/health`, CORS, DNS and SPA routes. None
of those exercise a signed-in request, so the fault went unnoticed and the
"production Worker is ready" conclusion was premature. Any future environment
sign-off must include one real authenticated request.

Fixed in kcms-backend `4b9f778`: fetch the key set with the async httpx client
already used in that module, cache it for ten minutes, refresh once on an
unknown `kid`, and return 503 rather than 401 when the key set cannot be
fetched so this can never again be mistaken for a bad token. Deployed to both
Workers and verified: staging `POST /api/v1/auth/clerk` returns 200, and the
staging database now holds 1 user, 1 identity, 1 workspace, 1 membership and
0 sandbox workspaces.

Second blocking defect, same day — pooled connections on Workers:

The first fix exposed a deeper one. `Database` built an asyncpg pool during
FastAPI startup, which on a Worker runs inside whichever request reached the
isolate first. Workers forbid reusing an I/O object across requests, so every
later request on that isolate blocked on a socket it did not own until the
runtime cancelled it with "your Worker's code had hung and would never generate
a response". A 110-event capture showed 28 of 31 `POST /auth/clerk` hung, plus
hangs on `/comments`, `/comments/summary`, `/settings` and
`/facebook/connections` — the occasional success was a fresh isolate.

Fixed in kcms-backend `5b76753`: a per-request connection mode. Startup opens
nothing; `acquire()` opens and closes one connection per use; the Worker
refreshes the DSN each request. Hyperdrive pools on Cloudflare's side. Render
keeps the pooled path, so the rollback target is unchanged. All 75 `acquire()`
call sites were left untouched.

Verified on staging with a full 52-event capture, 0 5xx and 0 hangs:
sign-in 200, sign-out 204, comments 200, settings 200, team 200, keywords 200,
auto-reply rules/events/settings 200, facebook connections 200, and
`POST /facebook/oauth/start` 201.

Third Workers defect, fixed 2026-09-16 — nested asyncio tasks:

`asyncio.wait_for` wraps its awaitable in a nested task, which Pyodide rejects
with `SystemError: Cannot enter a promising task from inside another running
promising task`. It surfaced as intermittent 500s on `OPTIONS
/api/v1/auth/clerk`: the CORS preflight failed, the browser refused to send the
POST, and the frontend fell back into its retry loop — the same visible symptom
as the two earlier defects but a different cause. Fixed by using asyncpg's own
`connect(timeout=...)`. Fifteen consecutive preflights then returned 200, where
seven of nine had failed before.

CPU decision — 2026-09-16, owner's call: stay on Workers Free for now.

`Worker exceeded CPU time limit` fired three times in one capture, with
measured `cpuTime` p50 34 ms, p90 64 ms, max 402 ms against the free limit of
10 ms per invocation. ADR-0008 names this as the stop condition for the
cutover. The owner chose to continue on the free plan and gather more evidence
rather than enable Workers Paid at $5/month.

Consequence to respect: free-tier enforcement is bursty, so a clean session is
not evidence the limit is satisfied. Do not treat any staging session as
cutover approval while this is open, and re-present the paid option if the CPU
errors recur under real use.

Open risk — CPU: observed `cpuTime` p50 36 ms, p90 107 ms, max 420 ms, against
the documented Workers Free limit of 10 ms per invocation. Nothing failed for
CPU in these captures, but the margin is the opposite of comfortable. Measure
again under real load before cutover, and treat Workers Paid ($5/month) as the
likely outcome.

Process note: a 6-event sample was read as success while the full capture
showed 28 hangs. Judge a Worker deployment only from a complete capture.

Still open: one `OPTIONS /api/v1/auth/clerk` returned 500 with "Worker's code
had hung and would never generate a response". It happened once, on the first
request after deployment. Watch for it; if it recurs outside cold start,
investigate before the production cutover.

Secret exposure, 2026-09-16 — resolved in part, remainder accepted by owner:

Two production secrets were briefly written as Cloudflare secret *names*, which
are listed in plaintext, during a mistaken `wrangler secret put` invocation.

- Meta app secret: rotated. Verified — Meta rejects the old value with
  "Error validating client secret".
- Clerk production secret key: **not rotated**. Verified still valid against
  `GET https://api.clerk.com/v1/users` (HTTP 200) after the exposure. The owner
  reviewed this and chose to accept the risk rather than rotate.

What accepting it means: that key is a full Clerk backend credential for the
production instance and can read, modify and delete production users. It exists
in the assistant transcript of 2026-09-16 and in Cloudflare's secret listing
history. If production user data is ever found altered unexpectedly, rotate
this key first and treat it as the likely cause.

BLOCKER before production cutover — Clerk identity provenance:

Live production has always run on the Clerk **development** instance
(`pk_test_...`), so the production database's Clerk identities carry
development-instance user IDs. Counts in Neon `findmoy-production`:

| provider | rows | sample `provider_id`                     |
|----------|------|------------------------------------------|
| email    | 19   | `chhuonnara002@gmail.com`                |
| clerk    | 7    | `user_3Iua1z2z7M9IfR7zwglBZ9whQqK`       |

That sample is the *same* Clerk user ID as the staging test user, which proves
the provenance.

The new production instance on `clerk.findmoy.app` issues different user IDs.
At cutover those 7 people would match no `identity` row, each receive a fresh
`app_user`, and lose access to their existing workspace, membership and comment
history. The 19 workspaces and 109 comments stay in the database but become
unreachable by their owners.

The mapping table and procedure are prepared in
`docs/2026-09-16-clerk-identity-remap.md`.

Resolve before cutover. Preferred: remap. Create the 7 users in the production
Clerk instance, build a verified email -> old ID -> new ID table, then
`UPDATE identity SET provider_id = <new> WHERE provider = 'clerk' AND
provider_id = <old>`. Only 7 rows. Keeps the pool isolation.

Alternatives: revert production to the development instance, which restores the
shared-pool problem ADR-0008 set out to fix; or accept re-registration, which
silently orphans the 7 accounts and is not acceptable.

The 19 `email` identities are legacy email/password accounts and are unaffected
by the Clerk instance change.

Platform administration is matched by email in `PLATFORM_ADMIN_EMAILS` and
reconciled by `_sync_platform_admin` on every sign-in, so it survives the
instance change and needs no remap. Set to
`chhuonnara002@gmail.com,kcms@uberip.com` on staging.

Next:

1. Set the production Worker `CLERK_SECRET_KEY` to the Clerk **production**
   instance secret (`sk_live_...`). It currently holds the development
   instance key, so production token verification will fail until this is done.
2. Set staging Worker `META_APP_ID`, `META_APP_SECRET`, `META_LOGIN_CONFIG_ID`
   from the production Meta app, per the owner override in ADR-0008.
3. Add both callback URLs to the Meta app's valid OAuth redirect URIs:
   `https://api-staging.findmoy.app/api/v1/facebook/oauth/callback` and
   `https://api.findmoy.app/api/v1/facebook/oauth/callback`.
4. Verify staging sign-in, authenticated API reads, invitation/setup links,
   Meta OAuth, and Sentry environment.
5. Production cutover, only after staging passes and with explicit approval:
   fast-forward `main` to `staging` in both repositories, attach
   `api.findmoy.app` to the production Worker, attach `findmoy.app` and
   `www.findmoy.app` to the `findmoy-production` Pages project, then verify.
6. Only after production verification: remove the Vercel project and the Render
   service and database.

Do not delete Vercel or Render before step 5 passes. `findmoy.app` is currently
only a Namecheap parking redirect; the live product is still
`kcms-frontend.vercel.app` on `kcms-backend.onrender.com`.
