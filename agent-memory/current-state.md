# Current State

**Updated:** 2026-08-30

**Active part:** Part 0 - Foundation

**Part status:** Planning foundation in progress; application scaffolding has not
started.

## Repository State

- `kcms-planning`: local Git repository on `main`; planning foundation committed
  at `f75c166`; no remote configured.
- `kcms-frontend`: no application scaffold; agent workflow committed on `main` at
  `627e80c`; remote `git@github.com:havbay/kcms-frontend.git` configured, remote
  main not present.
- `kcms-backend-`: no application scaffold; agent workflow committed on `main` at
  `85dbfdf`; remote `git@github.com:havbay/kcms-backend-.git` configured, remote
  main not present.
- KCMS v1: unchanged on `feature/kcms-full-redesign-bilingual`; it is reference
  evidence and not the V2 implementation base.

## Confirmed Evidence

- `source-confirmed`: both V2 code repositories contain only their root agent
  workflow and Git metadata; no runtime code exists.
- `source-confirmed`: frontend and backend use independent Git repositories.
- `source-confirmed`: backend local directory and remote currently include a
  trailing hyphen.
- `runtime-confirmed`: Codex CLI is `0.151.0`.
- `runtime-confirmed`: OpenDesign uses isolated Node `24.18.0` and pnpm
  `10.33.2`; the workstation default Node installation was not changed.
- `runtime-confirmed`: OpenDesign `0.21.1` runs from upstream `main` commit
  `df84ae5` with web on `127.0.0.1:5180` and daemon on `127.0.0.1:7456`.
- `runtime-confirmed`: the official OpenDesign Codex plugin `0.5.3` is installed
  and enabled, and its registered MCP completed a 22-tool handshake.
- `runtime-confirmed`: OpenDesign's live Claude Code catalog includes Claude 5
  aliases and explicit Opus 5, Sonnet 5, and Fable 5 options.

## Blockers And Decisions Needed

- Confirm whether `kcms-backend-` and `kcms-backend-` on GitHub are intentional;
  do not rename or change the remote without owner approval.
- Establish `kcms-planning` as a Git repository and choose its remote.
- Accept the product specification and choose backend/frontend runtimes through ADRs.

## Last Verified Outcome

The planning source of truth, vertical-slice roadmap, OpenDesign workflow, and
agent-memory structure are locally version-controlled. The OpenDesign source,
plugin, MCP, daemon, and web UI are runtime-verified. Both code repositories contain
the canonical-memory pointer. No V2 application behavior is claimed.
