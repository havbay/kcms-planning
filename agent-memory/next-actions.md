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
10. Add Platform Administration for workspaces, users, integration health, and
   audit logs without exposing ordinary customer comment content.
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
