# KCMS V2 Design System

**Status:** Draft outline

This file is the canonical design source of truth for KCMS V2. The sections below
define what must be completed and approved before frontend implementation depends
on the design system. Unspecified values remain `TBD`; do not infer or silently
invent them in production code.

## 1. Product Identity

- KCMS name and purpose: `TBD`
- Trustworthy, calm, operational tone: `TBD`
- Logo and brand usage: `TBD`

## 2. Colors

- Brand palette: `TBD`
- Neutral interface colors: `TBD`
- Severity colors:
  - `SAFE`: `TBD`
  - `OFFENSIVE`: `TBD`
  - `HARMFUL`: `TBD`
- Target indicators:
  - `PERSON`: `TBD`
  - `INSTITUTION`: `TBD`
  - `NEITHER`: `TBD`
- Success, warning, error, disabled, and focus states: `TBD`

## 3. Typography

- English and Khmer font families: `TBD`
- Heading, body, label, and table styles: `TBD`
- Rules for long Khmer text: `TBD`

## 4. Layout

- Sidebar and header structure: `TBD`
- Desktop, tablet, and mobile breakpoints: `TBD`
- Dashboard density, spacing, and content widths: `TBD`

## 5. Components

- Buttons, inputs, tables, tabs, filters, and dialogs: `TBD`
- Status badges, confidence indicators, and comment rows: `TBD`
- Loading, empty, denied, error, stale, and retry states: `TBD`

## 6. Operational Workflows

- Landing page and request access: `TBD`
- Authentication and invitations: `TBD`
- Client work list and human comment review: `TBD`
- Client summary, Page connection, Team, and Policy: `TBD`
- Platform Administrator onboarding, health, and audited support: `TBD`

## 7. Interaction Rules

- Human actions must be reversible.
- Hiding a comment is separate from correcting its label.
- Severity and target remain independent.
- Automatic hiding is not presented as active before validation.

## 8. Localization

- English and Khmer support: `TBD`
- Flag-based language switcher: `TBD`
- Text expansion and Khmer line-height rules: `TBD`

## 9. Accessibility

- WCAG AA contrast: `TBD`
- Keyboard and visible focus support: `TBD`
- Reduced motion: `TBD`
- 200 percent zoom and responsive text fitting: `TBD`

## 10. Design Restrictions

- No unsupported analytics or fake production data.
- No excessive cards, gradients, or decorative clutter.
- No role or permission controls the backend does not support.

## Approval Gate

This design system is ready for implementation only after the team replaces the
required `TBD` entries, reviews English and Khmer behavior, and changes the status
from `Draft outline` to `Approved`.
