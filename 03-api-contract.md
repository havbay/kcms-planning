# KCMS V2 API Contract Conventions

## Authority

The backend-generated OpenAPI 3.1 document is the executable contract. This file
defines conventions that the generated document must follow. The frontend client
is generated from an accepted OpenAPI artifact and must not hand-maintain duplicate
request or response interfaces.

## Base Contract

- Base path: `/api/v1`
- Media type: `application/json` unless an export endpoint documents another type.
- Identifiers: opaque strings; clients do not infer type or ordering from them.
- Timestamps: UTC RFC 3339 strings with an explicit offset.
- Enum values: stable uppercase domain values where defined by the product spec.
- Unknown response fields are tolerated by clients; removed or retyped fields are
  breaking changes.

## Authentication

- Browser authentication uses a bearer token in `Authorization`, not a cookie.
  The frontend and API are served from different sites, where `SameSite=None`
  cookies are blocked by default in several browsers. See D-022.
- Only the SHA-256 of a session token is stored server-side.
- `GET /api/v1/auth/me` returns the signed-in identity.
- `GET /api/v1/auth/providers` reports which sign-in providers are configured, so
  the frontend never renders a provider that cannot work.
- Invalid or expired sessions return `401`.
- Authenticated users without a capability receive `403`.
- Resource identifiers outside the user's workspace return `404`, not `403`. A
  `403` would confirm the resource exists in someone else's workspace.

## Part 0 Health Boundary

`GET /api/v1/health` is public and database-aware. Its `operationId` is
`getHealth`. A ready response returns `200`:

```json
{
  "service": "kcms-backend",
  "status": "READY",
  "database": "REACHABLE",
  "contract_version": "1.0.0"
}
```

When PostgreSQL cannot answer the probe, the endpoint returns the same schema with
`503`, `status` equal to `DEGRADED`, and `database` equal to `UNREACHABLE`. It does
not return exception or connection details.

The accepted artifact first enables frontend-first development. The frontend may
simulate this operation only at the network boundary in tests or an explicitly
enabled local preview. The later backend implementation exports the deterministic
artifact, the frontend vendors that accepted revision and regenerates its client,
and Part 0 completes only after the live endpoint replaces simulation.

## Error Envelope

```json
{
  "error": {
    "code": "stable_machine_code",
    "message": "Safe user-facing summary",
    "request_id": "opaque-request-id",
    "fields": {
      "email": ["validation_message"]
    }
  }
}
```

`fields` is omitted when the failure is not field-specific. Stack traces, SQL,
tokens, and private identifiers are never returned.

## Collections

Collection endpoints use cursor pagination:

```json
{
  "items": [],
  "next_cursor": null,
  "partial": false,
  "generated_at": "2026-08-30T00:00:00Z"
}
```

Filters use documented query parameters. Sort order is stable and includes an
identifier tie-breaker.

## Mutations

- Creation returns `201` and the created resource.
- Accepted asynchronous work returns `202` and an operation identifier.
- Successful deletion with no representation returns `204`.
- Retriable creation and external side-effect requests accept an idempotency key.
- Optimistic concurrency uses a version field or conditional request documented by
  the resource; clients never silently overwrite a newer policy.

## Contract Change Process

1. Define and accept the intended backend-owned OpenAPI change.
2. Vendor the accepted artifact and regenerate the frontend client.
3. Add frontend success and failure-state tests at the network boundary.
4. Implement and test the frontend workflow against that contract.
5. Update the backend contract test, implement the behavior, and export the
   deterministic artifact.
6. Review the OpenAPI diff and regenerate the frontend client if the export
   changed without changing the accepted semantics.
7. Add allowed and denied backend behavior tests where authentication applies.
8. Run the live cross-repository Playwright journey.
9. Record the verified contract revision in `agent-memory/integration-state.md`.


## Part 1 Page Connection Requests

A sandbox workspace asks to connect a real Facebook Page; a Platform
Administrator decides. Approval lifts `workspace.is_sandbox`. It does not itself
connect a Page: Meta OAuth is a later slice.

### Client

`POST /api/v1/access-requests` — `operationId: createAccessRequest`

```json
{
  "page_name": "facebook.com/angkorshop",
  "monthly_comments": "1K_TO_10K",
  "team_size": "2_TO_5",
  "note": "We get a lot of scam replies on product posts."
}
```

- `monthly_comments`: `UNDER_1K` · `1K_TO_10K` · `10K_TO_50K` · `OVER_50K`
- `team_size`: `JUST_ME` · `2_TO_5` · `6_TO_20` · `OVER_20`
- `note` is optional and bounded.
- Returns `201` with the created request.
- A workspace holds at most one open request. Submitting while one is `PENDING`
  replaces it, and still returns `201`.
- A workspace that is no longer a sandbox returns `409`.

`GET /api/v1/access-requests/mine` — `operationId: getMyAccessRequest`

Returns the workspace's latest request, or `204` when none exists. Includes
`status`, and `decision_reason` when declined.

### Platform Administrator

`GET /api/v1/admin/access-requests` — `operationId: listAccessRequests`

Optional `status` filter. Returns workspace name, requester display name and
email, Page, volume, team size, note, status, and decision metadata.

**This response never contains comment content, at any nesting level.** The
product specification forbids Platform Administrators from browsing customer
comments through ordinary administration views, and this is the first endpoint
where that rule is testable rather than aspirational.

`POST /api/v1/admin/access-requests/{id}/decision` — `operationId: decideAccessRequest`

```json
{ "decision": "APPROVED" }
{ "decision": "DECLINED", "reason": "Page is not currently reachable." }
```

- `reason` is required when declining and rejected as `422` when absent.
- Approving sets `workspace.is_sandbox = false`.
- Deciding an already-decided request returns `409`.
- Both admin endpoints return `403` for a signed-in non-administrator.

### Platform Administrator identity

Platform Administration is a platform-level role and is not expressible through
`membership.role`, which scopes a user to one workspace.

`app_user.is_platform_admin` is set from a `PLATFORM_ADMIN_EMAILS` environment
allowlist when a matching account signs in. It is never settable through the API,
so the role cannot be self-assigned or escalated by any request.

## Client Facebook Page Connection

All endpoints require a Client session and an approved, non-sandbox workspace.
One workspace has at most one active Page Connection. Provider credentials are
encrypted at rest and never occur in a response.

| Method | Path | operationId |
|---|---|---|
| `GET` | `/api/v1/facebook/connection` | `getFacebookConnection` |
| `DELETE` | `/api/v1/facebook/connection` | `disconnectFacebookPage` |
| `POST` | `/api/v1/facebook/connections/manual` | `connectFacebookPageManually` |
| `POST` | `/api/v1/facebook/oauth/start` | `startFacebookAuthorization` |
| `GET` | `/api/v1/facebook/oauth/sessions/{state}` | `listFacebookPageChoices` |
| `POST` | `/api/v1/facebook/oauth/sessions/{state}/selection` | `selectFacebookPage` |

The manual request contains only `page_access_token`. KCMS validates it with the
provider and derives Page id, name, tasks, and capability. Facebook authorization
returns an authorization URL; its callback stores encrypted Page candidates and
redirects to the frontend with an opaque, single-use session state. The Client
then chooses one authorized Page.

Both methods return the same public shape: state, Page id/name, method, tasks,
`can_moderate`, connection time, and last synchronization time. The acquisition
method never grants capability that the Meta token does not have.

## Moderation Collection Depth

`GET /api/v1/comments` accepts:

- `limit` and `offset`
- `query`
- `severity`: `SAFE` · `OFFENSIVE` · `HARMFUL`
- `target`: `PERSON` · `INSTITUTION` · `NEITHER`
- `surfaced_reason`: `triage` · `institution_sample` · `novel_language` ·
  `uncertainty` · `cleared`
- `review_status`: `PENDING` · `ACTIONED`
- `sort`: `PRIORITY` · `NEWEST` · `OLDEST`

Each item includes `post_text`, `parent_text`, `is_reply`, `post_kind`, and an
optional `post_permalink`, in addition to verdict, Action, and Correction fields.
Sort order always includes `comment_id` as its final tie-breaker. Source context
is customer content and must never leak into ordinary Platform Administration
responses.
