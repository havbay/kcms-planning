# Moderation Actions: Restoring Hide

**Status:** draft — not approved, do not implement

## Why

The action set is currently `LEAVE` and `DELETE`. A moderator facing a comment
they are unsure about has to choose between doing nothing and destroying it
permanently. There is no middle.

Hiding is that middle, and it is the option that fits a rule-based classifier.
The matcher will be wrong sometimes; a reversible action is what makes acting on
its output safe. Deletion cannot be undone on Facebook, so every irreversible
choice made on an uncertain signal is a bet that cannot be unwound.

## Proposed action set

| Action | Effect on Facebook | Reversible |
|---|---|---|
| `LEAVE` | nothing | n/a |
| `HIDE` | visible only to its author and their friends | yes, via `UNHIDE` |
| `UNHIDE` | restores a hidden comment | yes |
| `DELETE` | removed permanently | **no** |

`UNHIDE` ships with `HIDE` or neither. Hiding without a way back is a one-way
door wearing the label of a reversible action, which is worse than not offering
it.

`LEAVE` stays meaningful: it records that a person looked and decided nothing was
needed. That is a different fact from nobody having looked, and the queue depends
on the difference.

## What already exists

- `set_comment_hidden` is still implemented on the Graph client and unused.
- The `action` table already permits `HIDE` and `UNHIDE`; migration 015 kept them
  so historical rows stay valid.
- `provider_applied` already records whether an action reached Facebook.

So this is mainly widening `ActionKind` from `Literal["LEAVE", "DELETE"]`,
routing the two kinds to the existing provider call, and the buttons.

## Constraints, verified against the live Page

**A Page cannot hide its own comments.** Meta reports `can_hide: false` and
refuses with `(#200) Can not hide or unhide this comment`. It *will* delete the
same comment. This is not hypothetical — it is what happened when the hide round
trip was tested on KCMS-Demo, where every comment had been posted by the Page.

That matters for the interface: offering Hide on a comment Facebook will not hide
sets the moderator up to fail at the moment they act. Either `can_hide` is read
at ingest and stored so the button can be absent, or the refusal is surfaced
clearly on the row. Storing it costs a column and a field in the fetch.

**Both actions need `pages_read_engagement` and `pages_manage_engagement`.**
`pages_manage_engagement` alone is refused with `(#200) Requires
pages_read_engagement permission to manage the object`. `can_moderate` already
requires both.

## Safe comments stay hidden

Confirmed as intended, not a defect. Comments routed to `cleared` are stored and
classified but never returned by the work list. The queue is a queue; safe is the
large majority and would bury the rest.

Two consequences to handle:

**Remove the dead filter.** `surfaced_reason=cleared` is offered in the API and
the "Why surfaced" dropdown, but cleared rows are excluded before any filter
applies, so choosing it always returns nothing. Now that hiding cleared is a
settled decision, the option should go rather than be made to work.

**This does not contradict the sampling draft.** A sampled comment carries
`cleared_sample`, a different reason from `cleared`. The cleared population stays
out of the dashboard; a small labelled sample is a deliberate exception, and the
separate reason is what keeps the two distinguishable in the record.

## Open

**Whether severity suggests an action.** Offensive to hide and harmful to delete
is the obvious mapping, but a suggested default on an irreversible action is a
nudge toward the one choice that cannot be taken back. Suggesting `HIDE` and
never `DELETE` is the safer asymmetry, if anything is suggested at all.

**Whether `can_hide` is stored.** Needed to hide the button rather than let the
action fail. Costs a column, a field in the comment fetch, and staleness — the
value is true at ingest and Facebook may change it later.

**Whether `UNHIDE` appears only on hidden comments.** Showing it on a comment
that was never hidden is noise; hiding it means the row's controls change shape
depending on state, which is harder to scan quickly in a dense table.

**What happens to a deleted comment's row.** The comment is gone from Facebook
but its record remains. Whether it stays in the queue, moves to a history view,
or disappears once actioned is not decided.
