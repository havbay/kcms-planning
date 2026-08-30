# KCMS V2 OpenDesign Workflow

## Role In The Project

OpenDesign is the visual discovery, design-system, prototype, and design-handoff
workspace for KCMS V2. It does not replace the production frontend, backend, API
contract, or automated verification.

```text
Product specification
        |
        v
OpenDesign brief + KCMS DESIGN.md
        |
        v
Approved runnable reference artifacts
        |
        v
kcms-frontend implementation <--- generated OpenAPI client <--- kcms-backend
        |
        v
Playwright same-frame comparison and live journey
```

## How The Local Integration Works

The official Codex integration uses this boundary:

```text
Codex plugin
    -> local OpenDesign MCP
    -> local OpenDesign runtime
    -> selected local agent or explicitly selected cloud agent
```

The plugin does not replace or bundle OpenDesign. OpenDesign must be installed and
its local MCP must be registered with Codex. On this Ubuntu workstation, use the
official source installation because the signed desktop downloads target macOS and
Windows.

The current verified pre-install state is:

- Codex CLI: `0.151.0`, compatible with the plugin minimum.
- Node.js: `22.22.3`, below OpenDesign's required Node 24.
- pnpm: `9.15.9`, below the required `10.33.2`.
- OpenDesign Codex plugin: not installed.
- OpenDesign MCP: not registered.
- `/usr/bin/od`: unrelated Linux octal-dump utility; never use it as OpenDesign.

Part 0 upgrades the isolated OpenDesign toolchain, installs a pinned OpenDesign
release outside the KCMS code repositories, installs the official Codex plugin,
registers the local MCP using the actual OpenDesign CLI, and verifies the handshake.
Optional Vela or cloud login is a separate user decision and is not part of local
installation.

## Slice Workflow

### 1. Collect The Brief

For the active part, provide OpenDesign:

- The product goal and active user.
- The exact start and successful end states.
- Required capabilities and forbidden information.
- English and Khmer copy requirements.
- Real API field shapes without credentials or customer data.
- Required loading, empty, denied, error, stale, partial, and success states.
- Target desktop, tablet, and mobile viewports.

### 2. Apply The KCMS Design System

One canonical `DESIGN.md` controls:

- KCMS brand and voice.
- Color and semantic-state usage.
- Khmer-capable typography.
- Spacing, density, borders, radii, and elevation.
- Navigation and operational layout.
- Form, table, status, dialog, and feedback patterns.
- Accessibility, responsive, and anti-pattern rules.

Do not create a new visual direction for each page. A proposed system change must
be approved and incorporated into `DESIGN.md` before another slice depends on it.

### 3. Generate The Complete Slice

Generate individual artifacts for every required screen and state. Use synthetic,
clearly non-production content that exercises long Khmer labels and realistic
layout density. Never use real customer names, comments, email addresses, Page
tokens, or authenticated captures.

### 4. Review Before Code

The team reviews:

- Task clarity and navigation.
- Brand consistency.
- Role and privacy boundaries.
- English and Khmer text fit.
- Keyboard and focus behavior.
- Mobile and desktop composition.
- Loading, empty, failure, and recovery behavior.
- Whether every visible primary control has defined behavior.

Rejected artifacts stay out of the frontend repository.

### 5. Freeze The Handoff

Approved output is copied to the frontend repository:

```text
docs/design/
├── DESIGN.md
└── part-N-name/
    ├── brief.md
    ├── acceptance.md
    ├── desktop/
    ├── mobile/
    └── states/
```

The handoff records the OpenDesign project identity, generation date, viewport,
language, state, and artifact filename. Secrets and OpenDesign authentication state
are excluded.

### 6. Implement Against Real Contracts

Codex implements the approved experience in `kcms-frontend`. Production screens
use the generated API client and real backend responses. OpenDesign-generated
backend behavior, authentication assumptions, and sample analytics are ignored
unless they already exist in the accepted product and API specifications.

### 7. Verify The Match

Playwright captures the production implementation at the same viewport and state.
The reference and implementation are placed in one comparison frame. Layout,
typography, controls, responsiveness, accessibility, console output, and the live
end-to-end journey must pass before the slice is complete.

## Initial KCMS Brief For OpenDesign

```text
Design KCMS V2, a bilingual English and Khmer operational platform for moderating
Khmer and Khmerlish Facebook comments. The product helps customer staff find
targeted abuse and scams while preserving legitimate criticism of institutions.

The users are Operators, Administrators, Moderators, and blind Annotators. Use a
calm, credible trust-and-safety design system optimized for repeated operational
work. Keep Severity and Target visibly independent. Make human actions reversible
and do not imply that hiding a comment is a label correction.

Create only the active vertical slice. Include desktop, tablet, mobile, English,
Khmer, loading, empty, denied, error, retry, and success states required by its
acceptance criteria. Use synthetic content and no unsupported analytics. Every
primary control must have a defined interaction. Respect WCAG AA, keyboard use,
200 percent zoom, reduced motion, and long Khmer text.
```
