# Integration State

The backend-generated `openapi.json` is authoritative. A backend test checks
byte equality, and the frontend vendors that artifact and regenerates
`src/api/schema.d.ts`; production frontend modules do not import fixtures.

## Environments

| Component | URL |
|---|---|
| Frontend | https://kcms-frontend.vercel.app |
| Backend | https://kcms-backend.onrender.com |
| Database | Render PostgreSQL 16, Singapore |

The onboarding, Page Connection, and expanded moderation contract are deployed.
Frontend commit `a2c6d44` is live on Vercel and backend commit `4b5addb` is live
on Render.

## Verified contract boundary

- Authenticated Client Page Connection supports Facebook authorization and an
  advanced Page token. Controlled Meta test doubles verify both acquisition
  paths without making a live provider request.
- Facebook authorization emits the configured Facebook Login for Business
  `config_id`. The frontend distinguishes missing provider configuration from
  an unapproved workspace instead of failing silently.
- Both paths produce the same public connection representation and encrypted
  backend record; credentials never enter API responses.
- Comments expose source post/caption/type and optional parent context and accept
  server-side search/filter/sort/pagination parameters.
- The frontend consumes the regenerated contract and renders connection,
  compact moderation, context panel, Actions, and Corrections.
- Desktop and mobile browser checks show no page-level overflow.
- Production health is `READY/REACHABLE`; deployed OpenAPI contains the new
  connection and moderation fields, and protected endpoints reject anonymous
  requests with `401`.
- The production JavaScript contains the explicit Meta configuration and
  reviewed-access and approval-request states. Render and Vercel report no error
  logs for the new release.
- Public signup is absent from the live OpenAPI contract. The maintained demo
  Platform Admin can begin Meta connection from its sandbox; ordinary sandboxes
  remain denied and can submit the approval request.

## Live evidence still required

1. Sign in with the maintained demo Platform Admin account, authorize a Page the
   same app-role account administers, and confirm Page discovery/tasks.
2. Synchronize a controlled video post and its comment.
3. Confirm pattern matching surfaces the synchronized comment.
4. Hide and unhide through KCMS, then verify the result on Facebook.
5. Disconnect/reconnect and prove credential-loss recovery.

## Testing seam

Frontend tests intercept at the network boundary. Backend tests replace the
`MetaClient` protocol. Real provider behavior is not inferred from either seam.
