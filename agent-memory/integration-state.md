# Integration State

**Contract:** The backend generates `openapi.json` from the application factory.
A test regenerates it and compares bytes, so drift fails deterministically.

**Generated frontend client:** `kcms-frontend/src/api/schema.d.ts`, produced by
`npm run api:generate`, which copies the backend artifact and runs
`openapi-typescript`. The file is committed and never hand-edited.

`openapi-typescript` is invoked through `npx` rather than installed as a
dependency: it peers on TypeScript 5 while the frontend uses 6. The generated
types are committed, so no build needs the tool.

## Live environments

| | URL |
|---|---|
| Frontend | https://kcms-frontend.vercel.app |
| Backend | https://kcms-backend.onrender.com |
| Database | Render PostgreSQL 16, Singapore, free plan |

Both halves are live and talking to each other. `CORS_ORIGINS` on the backend
allows the Vercel origin.

The pilot-onboarding contract is currently verified only in the local stack. It
must not be counted as production evidence until the frontend and backend
branches are pushed, Render finishes the new deploy, and the Vercel flow is
retested against it.

## Verified end to end in production

- Sign-up creates an isolated workspace seeded with its own comments.
- The work list loads from PostgreSQL and paginates.
- Actions persist and are attributed to the signed-in person by name.
- Corrections persist without acting on the comment.
- Two accounts cannot see or affect each other's comments; a foreign id gives 404.
- A non-administrator is redirected away from `/admin/requests` and receives 403
  from the administration endpoints.

## Local development

```
backend    uv run uvicorn kcms.app:app --reload      127.0.0.1:8000
frontend   npm run dev                               127.0.0.1:5173
database   docker compose up -d                      127.0.0.1:5432
```

`VITE_API_BASE_URL` selects the API. Vite loads `.env.development` **after**
`.env.local`, so `.env.development.local` is the file that overrides in dev.

CORS is port-exact: running the frontend on 5174 against a backend allowing 5173
produces a `400` preflight, not an obvious error.

## Locally verified pending slice

- A visitor submits `POST /api/v1/pilot-requests` without authentication.
- Only a Platform Administrator can list and decide pilot requests.
- Approval creates a one-time owner setup invitation without emailing a password.
- `/setup/:token` previews and accepts the invitation, establishes a session,
  and rejects reused, revoked or expired links.
- SMTP is optional; delivery state is audited and the administrative manual-link
  fallback keeps the flow operable without a provider.
- The updated OpenAPI artifact and generated frontend types match the boundary.

## Testing seam

Frontend tests intercept at the network boundary (D-016); production modules
import no fixtures. The Playwright stub must be extended whenever an endpoint is
added, or pages that call it render nothing and unrelated tests fail.

Playwright runs six browsers in parallel and fails with
`Object with guid ... was not bound in the connection` under machine load. That
is resource exhaustion, not a defect; re-run with `--workers=1`.
