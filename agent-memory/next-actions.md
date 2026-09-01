# Next Actions

Ordered by dependency and value. No dates.

## Publish the current slice after owner review

1. Review the local Page Connection and moderation interfaces.
2. Commit and push planning, backend, and frontend branches only after approval.
3. Deploy backend first, verify migrations and the live OpenAPI artifact, then
   deploy the frontend and retest the contract.

## Prove Facebook end to end

4. Configure the Meta application, callback URL, explicit Graph version,
   permissions, and `INTEGRATION_ENCRYPTION_KEY` in Render.
5. Test **Continue with Facebook** against a Page the owner administers. Keep the
   manual Page-token method as the assisted fallback.
6. Implement the `CommentSource` synchronization adapter and webhook boundary.
7. Run the agreed proof: publish a video, add a Khmer comment, synchronize it,
   surface it through PatternMatcher, hide/unhide it in KCMS, and verify the
   provider state on the source post.

## Complete moderation operations

8. Add full append-only moderation history and provider reconciliation.
9. Add failure/retry states for expired credentials, provider rate limits, and
   unavailable webhooks.
10. Add Random Audit only when real traffic exists to sample.

## Product operations

11. Configure transactional SMTP when a verified sender domain is available;
    the audited manual setup-link fallback remains valid until then.
12. Add Platform Administration for workspaces, users, integration health, and
    audit logs without exposing ordinary customer comment content.
13. Add workspace switching before one user manages multiple organizations.

## Model track

14. Write the Khmer annotation guideline and collect authorized manual seed data.
15. Keep training and evaluation conversations separated, train offline, and
    deploy only a version that passes false-suppression and missed-harm gates.
16. Corrections feed a reviewed future training round; the live system never
    retrains automatically.

## Deliberately deferred

Telegram alerts, Messenger/Instagram, suggested replies, buying-intent labels,
and controlled automatic hiding. Generic sales-performance tracking remains out
of scope.
