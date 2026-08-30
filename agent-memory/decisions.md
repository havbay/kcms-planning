# Accepted Decisions

## D-001: Greenfield KCMS V2

KCMS V2 is a new implementation. KCMS v1 is read-only reference evidence; code is
ported only when the V2 specification explicitly requires and tests the behavior.

## D-002: Separate Application Repositories

Frontend and backend use independent repositories, CI, dependencies, and releases.
See `adr/0001-separate-frontend-and-backend-repositories.md`.

## D-003: Canonical Planning Memory

Shared product requirements, architecture decisions, implementation order, and
handoff state live in the sibling `kcms-planning` repository.

## D-004: Vertical Slices

Work proceeds through Parts 0-8, one active part at a time. A frontend-only screen
or backend-only endpoint is not a completed part.

## D-005: OpenDesign Before Frontend Implementation

Every frontend slice receives an approved OpenDesign handoff before production UI
implementation begins. OpenDesign output does not define backend behavior.

## D-006: Real Production Data Boundaries

Production frontend code renders backend responses. Synthetic data is restricted
to tests, scripted local integration adapters, and clearly labelled design artifacts.

## D-007: Domain Invariants Survive The Rebuild

The two-axis taxonomy and separation of Verdicts, Actions, Corrections, and
Annotations remain mandatory regardless of backend technology.
