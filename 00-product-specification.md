# KCMS V2 Product Specification

## Product Goal

KCMS helps Cambodian organizations moderate Khmer and Khmerlish Facebook comments
at scale while preserving legitimate complaints and criticism.

The system classifies comments, routes the comments that need attention to a
human, records reversible moderation actions, and collects explicit human labels
for later model-training cycles.

## MVP Users

- **Visitor:** learns about KCMS and submits a reviewed access request.
- **Operator:** creates and supports customer workspaces across the service.
- **Administrator:** moderates a Page and manages its team and policy.
- **Moderator:** reviews surfaced comments and takes reversible actions.
- **Annotator:** labels comments without seeing the model Verdict.
- **Audience member:** writes Facebook comments but never uses the dashboard.

## Canonical Customer Journey

1. A visitor submits a request for access.
2. An Operator reviews the request and creates a workspace.
3. The Operator invites the first Administrator.
4. The Administrator sets a password and signs in.
5. The Administrator connects a Facebook Page and invites staff.
6. Ingestion receives comments from the connected Page.
7. The classifier produces a versioned Verdict.
8. Routing either clears the comment, surfaces it to a Work List lane, or records
   an eligible automatic action when the Page has explicitly enabled it.
9. A Moderator may leave, hide, or unhide the comment and may separately submit a
   label Correction.
10. Annotators create blind Annotations for training or evaluation datasets.
11. Corrections and Annotations feed a later, explicit model-training cycle.

## Classification Contract

Classification has two independent axes:

- Severity: `SAFE`, `OFFENSIVE`, `HARMFUL`
- Target: `PERSON`, `INSTITUTION`, `NEITHER`

A Verdict records both labels, an independent confidence for each axis, an
abstention state, a rationale when available, and the model version.

## Invariants

- A Verdict, moderation Action, Correction, and Annotation are separate records.
- Hiding a comment never implies a label Correction.
- Actions are reversible, attributable, and append-only.
- Annotators never see model Verdicts for comments they label.
- An Annotator cannot also moderate the same Page.
- Operators cannot browse customer comment content through ordinary operator views.
- Evaluation data never enters a training dataset.
- A Page-directed or institution-directed complaint is never automatically hidden.
- Automatic hiding is disabled by default and remains disabled during initial
  shadow-mode validation.
- Corrections and Annotations do not retrain the model automatically.
- Erasable comment content is separated from immutable decision history.

## Work List Lanes

- **Triage:** highest-risk surfaced comments, ordered for harm reduction.
- **Review:** bounded low-confidence, abstained, and institution-directed samples.
- **Audit:** rotating samples of cleared and automatically acted-on comments.

Each surfaced record keeps the reason it reached a human.

## MVP Scope

- Public landing, request-access, invitation, sign-in, recovery, and sign-out.
- Operator request review, workspace creation, invitation, health, and audit tools.
- Customer workspace and Facebook Page connection.
- Comment ingestion through a replaceable source interface.
- Replaceable classifier interface with an explicitly disclosed development stub.
- Routing, Work List, Actions, Corrections, history, and Page Policy.
- Blind Annotation, skip reasons, progress, disagreements, and exports.
- Honest evaluation metrics with denominators and unavailable-data states.
- English and Khmer interfaces.
- Responsive and keyboard-accessible web experience.

## Out Of Scope For Initial MVP

- Public self-service signup.
- Billing automation.
- Native mobile applications.
- Multi-organization enterprise hierarchy above customer workspaces.
- Automatic model retraining.
- Automatic hiding before shadow-mode evidence and explicit customer approval.
- Fabricated production analytics or sample customer records.

## MVP Success Criteria

- The first Administrator can reach a real customer dashboard from an invitation.
- A real backend Work List can be loaded and acted upon from the frontend.
- Every role is denied capabilities it does not hold.
- The same API supports scripted local ingestion and a future Facebook adapter.
- False suppression and missed harm are measured separately when denominators exist.
- Three to five real Page moderators can complete the primary workflow without
  developer assistance.
