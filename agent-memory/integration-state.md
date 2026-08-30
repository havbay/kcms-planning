# Integration State

**Contract revision:** Part 0 health semantics are source-confirmed in
`03-api-contract.md`; no generated OpenAPI artifact exists yet.

**Generated frontend client:** None.

**Local frontend URL:** None.

**Local backend URL:** None.

**Database:** PostgreSQL is accepted for V2; no service is configured or verified.

**Live cross-repository checks:** None.

**OpenDesign integration:** Runtime-confirmed. OpenDesign `0.21.1` is running at
`http://127.0.0.1:5180`, its daemon is healthy at `http://127.0.0.1:7456`, and the
registered Codex MCP completed a 22-tool handshake. OpenDesign Cloud is not
configured or verified.

**Next integration action:** Approve the Part 0 OpenDesign handoff, implement the
frontend Client shell and health workflow first through contract-faithful network
simulation, then implement live database-aware backend health and replace the
simulation in the one cross-repository smoke command.
