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

PRODUCTION CUTOVER COMPLETED 2026-09-16.

Live on Cloudflare and verified:

| host | result |
|------|--------|
| `findmoy.app` | 200, valid TLS, SPA routes 200 |
| `www.findmoy.app` | 200, valid TLS |
| `api.findmoy.app` | READY, database REACHABLE, CORS allows findmoy.app |
| `staging.findmoy.app` / `api-staging.findmoy.app` | 200 / READY |

The served production bundle is `index-Dx6S5j_n.js`, fingerprint-identical to
the locally built artifact, and carries `pk_live_Y2xlcmsuZmluZG1veS5hcHAk` and
`https://api.findmoy.app`.

`main` was fast-forwarded to `staging` in both repositories. DNS: the apex
`A 162.255.119.156` and `www CNAME parkingpage.namecheap.com` were replaced by
CNAMEs to `findmoy-production.pages.dev`. Namecheap MX/SPF records were left
untouched.

Two findings from the cutover:

1. The `findmoy-production` Pages project is **disconnected from Git**, so
   pushing `main` triggers no build. The deployment was a direct upload via
   `wrangler pages deploy`. Reconnect it, or keep deploying by direct upload —
   but do not assume a push publishes production.
2. Vercel overrides `VITE_CLERK_PUBLISHABLE_KEY` with a project environment
   variable, so `kcms-frontend.vercel.app` rebuilt from `main` still serves
   `pk_test` while calling `api.findmoy.app`, whose issuer is now
   `clerk.findmoy.app`. Sign-in on the Vercel host therefore fails. Either set
   that variable to the `pk_live` key or retire the Vercel project.

Still open after cutover:

- The two administrator demo accounts are **not** remapped. `kcms@uberip.com`
  and `kcms01@uberip.com` must not sign in on production until
  `docs/2026-09-16-clerk-identity-remap.md` is applied, or Clerk creates
  duplicate `app_user` rows and the remap becomes a merge.
- The other five owners re-register by decision; their old workspaces stay in
  the database unreachable.
- Render `kcms-postgres` restore was never re-compared against the source, and
  the temporary firewall rule `110.235.254.164/32` is still open. Settle both
  before deleting anything on Render.
- CPU remains over the Workers Free limit; see the CPU decision above.

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
