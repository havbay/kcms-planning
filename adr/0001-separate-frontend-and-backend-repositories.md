# Separate Frontend And Backend Repositories

**Status:** accepted

**Date:** 2026-08-30

## Context

KCMS V2 is a greenfield rebuild. The team wants to redesign the entire user
experience while retaining freedom to change the backend implementation. The two
applications need independent dependency, CI, deployment, and release ownership.

## Decision

KCMS V2 uses separate `kcms-frontend` and `kcms-backend` Git repositories. A
sibling `kcms-planning` repository owns shared product specifications, accepted
architecture decisions, implementation sequencing, and agent handoff memory.

The backend owns the OpenAPI contract. The frontend consumes a generated client
from an accepted contract artifact.

## Alternatives Considered

- A monorepo would simplify atomic changes but would couple release and repository
  ownership more tightly than the team requested.
- Duplicating shared plans into both repositories would avoid a third planning
  repository but would create two competing sources of truth.

## Consequences

- Cross-repository changes require coordinated commits and explicit contract
  revisions.
- Each application can change technology and deploy independently.
- The planning repository must be cloned beside both code repositories for the
  complete agent workflow.

## Verification

The local workspace contains independent frontend and backend Git repositories.
Their empty initial state and remotes are recorded in `agent-memory/current-state.md`.
