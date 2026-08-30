# KCMS V2 Product Specification

## Product Goal

KCMS helps Cambodian organizations moderate Khmer and Khmerlish Facebook comments
at scale while preserving legitimate complaints and criticism.

The system classifies comments, routes the comments that need attention to a
human, records reversible moderation actions, and collects explicit human labels
for later model-training cycles.

The first delivery goal is a functional full-stack prototype: a real frontend,
backend, persistence layer, and human moderation workflow. It uses a clearly
disclosed deterministic pattern-matching classifier behind the same replaceable
interface intended for the later Khmer AI model.

## MVP Users

- **Visitor:** learns about KCMS and submits a reviewed access request.
- **Client:** manages its workspace, Page connection, team, settings, and comment
  moderation. All trusted client users initially share this one visible role.
- **Platform Administrator:** maintains KCMS, reviews access requests, manages
  client workspaces and users, tracks service and integration health, and provides
  audited support without ordinary access to customer comment content.
- **Audience member:** writes Facebook comments but never uses the dashboard.

Manual seed-dataset annotation is an internal model-development workflow, not a
client-facing MVP role.

## Canonical Customer Journey

1. A visitor submits a request for access.
2. A Platform Administrator reviews the request and creates a workspace.
3. The Platform Administrator invites the first Client user.
4. The Client sets a password and signs in.
5. The Client connects a Facebook Page and may invite trusted teammates.
6. Ingestion receives comments from the connected Page.
7. The disclosed pattern-matching classifier produces a versioned Verdict.
8. Routing prioritizes comments and surfaces uncertain or concerning comments for
   human review; it does not perform automatic Facebook moderation actions.
9. A Client may leave, hide, or unhide the comment and may separately submit an
   explicit label Correction.
10. Approved, explicitly labelled samples may become candidates for a later,
    curated model-training cycle.

## Classification Contract

Classification has two independent axes:

- Severity: `SAFE`, `OFFENSIVE`, `HARMFUL`
- Target: `PERSON`, `INSTITUTION`, `NEITHER`

A Verdict records both labels, an independent confidence for each axis, an
abstention state, a rationale when available, and the model version.

## Prototype Classifier And Model Path

- The functional prototype uses deterministic pattern matching, including
  disclosed Khmer, Khmer-slang, and Khmerlish rules where evidence supports them.
- The pattern matcher implements the production classifier interface and records
  its rule-set version on every Verdict.
- It automates classification, prioritization, and routing only. A human makes
  every Facebook moderation Action during the prototype and initial shadow mode.
- Before a trained model is introduced, an internal team creates an authorized,
  manually labelled seed dataset using a written KCMS labelling guideline.
- A later Khmer model is trained and evaluated offline. Human review, explicit
  version approval, and rollback remain required for every production model.
- Production feedback never retrains or changes the live model automatically.

## Data And Learning Boundary

- Client comments are operational customer data by default, not an automatic
  shared training dataset.
- A moderation Action is never used as a Severity or Target label.
- Only explicit Corrections or approved manual Annotations can become labelled
  dataset candidates, subject to the accepted client permission, privacy, and
  retention policy.
- Dataset candidates are reviewed, minimized, traceable to their source, and
  assigned to either training or evaluation before use.
- Evaluation samples never enter training data.

## Invariants

- A Verdict, moderation Action, Correction, and Annotation are separate records.
- Hiding a comment never implies a label Correction.
- Actions are reversible, attributable, and append-only.
- Platform Administrators cannot browse customer comment content through ordinary
  platform-administration views.
- Evaluation data never enters a training dataset.
- A Page-directed or institution-directed complaint is never automatically hidden.
- Automatic Facebook moderation Actions are disabled throughout the functional
  prototype and initial shadow-mode validation.
- Corrections and Annotations do not retrain the model automatically.
- Erasable comment content is separated from immutable decision history.

## Work List Lanes

- **Triage:** highest-risk surfaced comments, ordered for harm reduction.
- **Review:** bounded low-confidence, abstained, and institution-directed samples.
- **Audit:** rotating samples of cleared and completed human-reviewed comments.

Each surfaced record keeps the reason it reached a human.

## MVP Scope

- Public landing, request-access, invitation, sign-in, recovery, and sign-out.
- Platform Administrator request review, workspace creation, user support, health,
  and audit tools.
- Customer workspace and Facebook Page connection.
- Comment ingestion through a replaceable source interface.
- Replaceable classifier interface with an explicitly disclosed, versioned
  pattern-matching implementation.
- Routing, Work List, Actions, Corrections, history, and Page Policy.
- A simple Client summary dashboard covering connection health, processed,
  surfaced, reviewed, pending, review time, and moderation outcomes.
- Honest operational and evaluation metrics with denominators and unavailable-data
  states.
- English and Khmer interfaces.
- Responsive and keyboard-accessible web experience.

## Out Of Scope For Initial MVP

- Public self-service signup.
- Billing automation.
- Native mobile applications.
- Multi-organization enterprise hierarchy above customer workspaces.
- A dedicated client-facing Annotation workspace.
- Automatic public replies, Messenger automation, and other provider integrations.
- A production trained Khmer model before the manual dataset and evaluation gates
  exist.
- Automatic model retraining.
- Automatic hiding before shadow-mode evidence and explicit customer approval.
- Fabricated production analytics or sample customer records.

## MVP Success Criteria

- The first Client can reach a real customer dashboard from an invitation.
- A real backend Work List can be loaded and acted upon from the frontend.
- Every role is denied capabilities it does not hold.
- The same API supports scripted local ingestion and a future Facebook adapter.
- Every prototype Facebook moderation Action is explicitly made by a human.
- The pattern matcher is disclosed in the UI and replaceable without changing the
  moderation workflow.
- False suppression and missed harm are measured separately when denominators exist.
- Three to five real client users can complete the primary workflow without
  developer assistance.
