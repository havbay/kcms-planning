# Next Actions

Ordered by dependency and value. No dates: the product owner sets the pace.

## Blocked on the product owner

1. **Telegram sign-in** stays dormant until `TELEGRAM_BOT_TOKEN` and
   `TELEGRAM_BOT_USERNAME` are set in Render, and `/setdomain` points the bot at
   the Vercel domain.
2. **Render's GitHub webhook does not fire.** Deploys are triggered manually.
   Reconnecting the repository in the Render dashboard would fix it.
3. **Real Facebook testing.** Untested. Everything downstream of ingestion
   depends on knowing whether dev-mode Graph API access works on a Page the
   owner administers.

## Next in the moderation workflow

4. **Comment context.** `post_text` and `parent_text` exist in
   `comment_content` but are never populated. Context is what separates an
   insult from a quote, and a correction given without it is a weak training
   signal. Requires seeding realistic threads, or waiting for ingestion.
5. **Filters** on the work list by surfacing reason, severity and status. The
   routing already computes the categories; this is mostly interface.
6. **Full moderation history.** The reversible trail is stored and only the
   latest action is shown.

## Then

7. **Platform Administration beyond access requests:** workspaces, users, fleet
   health, audit log. Administration views must never expose comment content;
   the existing leak test is the pattern to follow.
8. **Replaceable ingestion source interface**, then the real Facebook adapter.
   The classifier seam exists; the ingestion port does not.
9. **Telegram bot for alerts.** A client should not have to remember to open a
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

- `/admin/requests` still uses the card layout and no longer matches the density
  of the moderation table.
- Someone who belongs to two workspaces cannot return to their sandbox: a joined
  team takes precedence and there is no workspace switcher.

## Deliberately not doing

Generic sales or marketing analytics. KCMS holds no orders or revenue, and Meta
provides reach and follower data free and better. Comment-derived commerce
signals are in scope; sales performance tracking is not.
