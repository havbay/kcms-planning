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
