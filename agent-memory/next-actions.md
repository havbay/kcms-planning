# Next Actions

Ordered by dependency and value. No dates: the product owner sets the pace.

## Blocked on the product owner

1. **Transactional email.** Create a Resend account, verify a sending subdomain,
   create an API key, and set `SMTP_HOST`, `SMTP_PORT`, `SMTP_USERNAME`,
   `SMTP_PASSWORD`, `SMTP_FROM_EMAIL`, `SMTP_FROM_NAME` and
   `PUBLIC_FRONTEND_URL` in Render. Until then, the audited manual setup-link
   fallback is functional.
2. **Review and publish the pending onboarding slice.** After owner approval,
   push both feature branches, deploy backend first, check the live contract,
   then deploy and verify the frontend.
3. **Telegram sign-in** stays dormant until `TELEGRAM_BOT_TOKEN` and
   `TELEGRAM_BOT_USERNAME` are set in Render, and `/setdomain` points the bot at
   the Vercel domain.
4. **Render's GitHub webhook does not fire.** Deploys are triggered manually.
   Reconnecting the repository in the Render dashboard would fix it.
5. **Real Facebook testing.** Untested. Everything downstream of ingestion
   depends on knowing whether dev-mode Graph API access works on a Page the
   owner administers.

## Next in the moderation workflow

6. **Comment context.** `post_text` and `parent_text` exist in
   `comment_content` but are never populated. Context is what separates an
   insult from a quote, and a correction given without it is a weak training
   signal. Requires seeding realistic threads, or waiting for ingestion.
7. **Filters** on the work list by surfacing reason, severity and status. The
   routing already computes the categories; this is mostly interface.
8. **Full moderation history.** The reversible trail is stored and only the
   latest action is shown.

## Then

9. **Platform Administration beyond request review:** workspaces, users, fleet
   health, audit log. Administration views must never expose comment content;
   the existing leak test is the pattern to follow.
10. **Replaceable ingestion source interface**, then the real Facebook adapter.
   The classifier seam exists; the ingestion port does not.
11. **Telegram bot for alerts.** A client should not have to remember to open a
   dashboard; this is the retention mechanism rather than a login feature. Needs
   an always-on instance, webhook secret verification, and single-use, short-lived
   deep-link tokens.

## Accepted but not scheduled

- **Buying-intent labelling.** Comments are the sales channel in Cambodia;
  price enquiries are leads lost in the noise. Same pipeline, one extra label.
  Turns the product from a cost centre into a revenue tool. Agreed as the
  strongest adjacent product, to follow real ingestion and a live pilot.
- **Mobile-first moderation.** The dashboard is desktop-first while the first
  market runs its business on a phone. Raised and deliberately deferred.
- **Suggested replies**, always human-approved. Messenger and Instagram.

## Known inconsistencies

- Request administration covers decisions but not workspace/user/fleet views.
- Someone who belongs to two workspaces cannot return to their sandbox: a joined
  team takes precedence and there is no workspace switcher.

## Deliberately not doing

Generic sales or marketing analytics. KCMS holds no orders or revenue, and Meta
provides reach and follower data free and better. Comment-derived commerce
signals are in scope; sales performance tracking is not.
