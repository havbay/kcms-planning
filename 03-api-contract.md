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

- Browser authentication uses an HttpOnly session cookie.
- `GET /api/v1/me` returns identity, capabilities, and accessible workspaces.
- Invalid or expired sessions return `401` with a stable error code.
- Authenticated users without a capability receive `403`.
- Resource identifiers outside the user's workspace do not disclose private data.

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

1. Update the backend contract test first.
2. Generate and review the OpenAPI diff.
3. Regenerate the frontend client.
4. Add allowed and denied backend behavior tests.
5. Add frontend success and failure-state tests.
6. Run the live cross-repository Playwright journey.
7. Record the verified contract revision in `agent-memory/integration-state.md`.
