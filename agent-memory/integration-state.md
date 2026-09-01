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
Frontend commit `be79387` is live on Vercel and backend commit `25f5dbc` is live
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
  workspace-approval messages. Render reports no error logs for the new release.

## Live evidence still required

1. Approve the KCMS workspace if it is still sandboxed, then authorize a Page
   the app-role account administers and confirm Page discovery/tasks.
2. Synchronize a controlled video post and its comment.
3. Confirm pattern matching surfaces the synchronized comment.
4. Hide and unhide through KCMS, then verify the result on Facebook.
5. Disconnect/reconnect and prove credential-loss recovery.

## Testing seam

Frontend tests intercept at the network boundary. Backend tests replace the
`MetaClient` protocol. Real provider behavior is not inferred from either seam.
