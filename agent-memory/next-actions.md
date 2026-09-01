# Next Actions

Ordered by dependency and value. No dates.

## Prove Facebook end to end

1. Configure the Meta application, callback URL, explicit Graph version,
   permissions, and `INTEGRATION_ENCRYPTION_KEY` in Render.
2. Test **Continue with Facebook** against a Page the owner administers. Keep the
   manual Page-token method as the assisted fallback.
3. Implement the `CommentSource` synchronization adapter and webhook boundary.
4. Run the agreed proof: publish a video, add a Khmer comment, synchronize it,
   surface it through PatternMatcher, hide/unhide it in KCMS, and verify the
   provider state on the source post.

## Complete moderation operations

5. Add full append-only moderation history and provider reconciliation.
6. Add failure/retry states for expired credentials, provider rate limits, and
   unavailable webhooks.
7. Add Random Audit only when real traffic exists to sample.

## Product operations

8. Configure transactional SMTP when a verified sender domain is available;
    the audited manual setup-link fallback remains valid until then.
9. Add Platform Administration for workspaces, users, integration health, and
    audit logs without exposing ordinary customer comment content.
10. Add workspace switching before one user manages multiple organizations.

## Model track

11. Write the Khmer annotation guideline and collect authorized manual seed data.
12. Keep training and evaluation conversations separated, train offline, and
    deploy only a version that passes false-suppression and missed-harm gates.
13. Corrections feed a reviewed future training round; the live system never
    retrains automatically.

## Deliberately deferred

Telegram alerts, Messenger/Instagram, suggested replies, buying-intent labels,
and controlled automatic hiding. Generic sales-performance tracking remains out
of scope.
