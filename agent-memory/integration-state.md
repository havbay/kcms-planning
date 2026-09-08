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

The onboarding, multi-Page Connection, and expanded moderation contract are
deployed. Frontend commit `5008e71` is live on Vercel after manual promotion and
backend commit `6f8ab19` is live on Render.

## Verified contract boundary

- Authenticated Client Page Connection supports Facebook authorization and an
  advanced Page token. Controlled Meta test doubles verify both acquisition
  paths without making a live provider request.
- Facebook authorization emits the configured Facebook Login for Business
  `config_id`. Any authenticated Client workspace can begin authorization; the
  frontend distinguishes missing provider configuration from other failures.
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
- Public signup remains absent from OpenAPI. The Page-approval request endpoints
  are also absent from the current contract; onboarding is the single KCMS
  approval boundary.
- The local OpenAPI contract now includes `/api/v1/auto-replies` for workspace
  settings, owner-managed rules, simulation, and decision-log reads. The
  frontend regenerated `src/api/schema.d.ts` from that artifact. Page sync now
  also returns controlled auto-reply counts and uses the Meta comment-reply
  edge only after owner confirmation. The API no longer exposes a separate
  preview mode. This slice is local only until the
  repositories are intentionally pushed and deployed.

## Live provider evidence

Facebook Login, Page discovery, controlled comment synchronization, and
reversible hide/unhide were observed against a real test Page. Remaining live
proof is multi-Page behavior across two independently authorized Pages and
disconnect/reconnect credential-loss recovery.

## Deployment caveat

Vercel still records `feature/landing-header-hero` as the automatic Production
Branch. The public alias currently serves `main` commit `5008e71` because its
preview was manually promoted. Change the project Git setting to `main` before
assuming future pushes are production deployments.

## Testing seam

Frontend tests intercept at the network boundary. Backend tests replace the
`MetaClient` protocol. Real provider behavior is not inferred from either seam.

## Trial Management regression

Clerk-created trial workspaces use plan `TRIAL`. The backend Page Connections
contract now includes that value; otherwise Management failed at
`GET /api/v1/facebook/connections` with a Pydantic validation error. The
backend regression test covers an active trial workspace returning an empty,
valid connection list.

The authenticated dashboard now polls connected Pages every 30 seconds while
visible. The frontend sends one sync request per connected Page and refreshes
Moderate after the background result; server-side webhook/worker ingestion is
still deferred.

Automated Replies uses the existing Page sync boundary. The PatternMatcher risk
gate and idempotent event log run before a provider reply; the owner confirms
the live feature, and provider failure is recorded without claiming success.
The rule preview is non-posting. Messenger and server-side worker/webhook
ingestion remain deferred.

Moderation action history now includes `provider_applied` in the generated
OpenAPI and frontend types. The frontend consumes that value immediately after
HIDE/UNHIDE instead of inferring provider success from connection state. This
change is local until both repositories are intentionally pushed and deployed.
