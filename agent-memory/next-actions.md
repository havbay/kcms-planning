# Next Actions

Ordered by dependency and value. No dates.

## Prove Facebook end to end

1. Sign in with an approved Client account and test **Continue with Facebook**
   using a Page the same Meta account administers. There is no second KCMS Page
   approval. Keep the manual Page-token method as the assisted fallback.
2. Implement the `CommentSource` synchronization adapter and webhook boundary.
3. Run the agreed proof: publish a video, add a Khmer comment, synchronize it,
   surface it through PatternMatcher, hide/unhide it in KCMS, and verify the
   provider state on the source post.

## Complete moderation operations

4. Add full append-only moderation history and provider reconciliation.
5. Add failure/retry states for expired credentials, provider rate limits, and
   unavailable webhooks.
6. Add Random Audit only when real traffic exists to sample.

## Product operations

7. Configure transactional SMTP when a verified sender domain is available;
   the audited manual setup-link fallback remains valid until then.
8. Add Platform Administration for workspaces, users, integration health, and
   audit logs without exposing ordinary customer comment content.
9. Add workspace switching before one user manages multiple organizations.

## Model track

10. Write the Khmer annotation guideline and collect authorized manual seed data.
11. Keep training and evaluation conversations separated, train offline, and
    deploy only a version that passes false-suppression and missed-harm gates.
12. Corrections feed a reviewed future training round; the live system never
    retrains automatically.

## Deliberately deferred

Telegram alerts, Messenger/Instagram, suggested replies, buying-intent labels,
and controlled automatic hiding. Generic sales-performance tracking remains out
of scope.


## Deployment reality

Neither Render nor Vercel auto-deploys. Every Render deploy in the service
history is triggered `api` or `manual`, never `commit`, despite `autoDeploy:
yes`. Vercel likewise sat on an old bundle across two pushes. Both are
downstream of the locked GitHub billing account.

Deploy by hand after every push:
- backend: Render MCP `trigger_deploy` on `srv-daa8uepf2nfc739j4eb0`
- frontend: `npx vercel --prod --yes` from `kcms-frontend` (CLI is authenticated)

Verify by fingerprint, not by timestamp: compare the live bundle name against
`dist/assets/index-*.js`, and the live `/openapi.json` against the committed
artifact. A rolling deploy will otherwise answer from the old instance.

## Required for the Page demo

`META_GRAPH_VERSION` and `INTEGRATION_ENCRYPTION_KEY` must be set on Render or
connecting a Page returns 503.
