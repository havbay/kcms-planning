# Controlled Facebook Comment Auto-Replies

**Status:** accepted
**Date:** 2026-09-07

## Context

The rule-based reply feature is needed for a controlled Facebook Page demo, but
the initial product boundary keeps provider actions human-controlled. A broad
automatic-reply rollout would expand Meta review, safety, and stale-content risk
before the team has production evidence.

## Decision

Allow a narrow, owner-controlled Facebook comment auto-reply path for the pilot:

- Replies are processed only for newly ingested comments from a connected Page.
- The workspace owner must enable automated replies and confirm the live-reply
  warning; there is no separate demo mode.
- Only `SAFE` comments matching the first enabled comments rule may receive a
  reply. There is no fallback reply, unsafe-message reply, automatic hide, or
  automatic delete.
- Each provider comment is idempotent in the append-only auto-reply event log.
  The event is marked provider-applied only after Meta confirms the reply.
- Provider failures degrade to a logged skipped event and do not claim that a
  reply was posted.
- Messenger, webhooks/background workers, additional providers, and general
  customer rollout remain deferred and are visibly marked under development.

This is an explicit carve-out from the initial human-only provider-action
boundary for one controlled comment-reply demo. It does not authorize automatic
Facebook moderation actions.

## Alternatives considered

- Keep the feature permanently in preview mode: safest, but it cannot
  demonstrate the approved live Facebook reply path.
- Enable replies for every matched message and channel: rejected because it
  expands risk before a measured pilot and would imply Messenger support.
- Use a server scheduler or webhook immediately: deferred until worker/webhook
  operations and Meta review are ready; the current demo uses existing Page sync.

## Consequences

The existing Page sync becomes the live comment-reply boundary and returns
reply counts. The UI makes the live scope and owner confirmation clear; its rule
preview never posts. The first pilot uses one workspace, one controlled Page,
one test rule, and unique test comments. Reply quality is measured against later
complaint labels, with a target of zero false replies.

## Verification

Backend tests cover live posting, safe unmatched silence, event recording,
provider failure handling, and duplicate sync protection. The
frontend tests cover owner confirmation and Messenger's disabled state. A real
Meta-side post remains a deployment/demo verification step, not a local test.
