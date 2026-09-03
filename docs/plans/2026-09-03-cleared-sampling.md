# Sampling Cleared Comments

**Status:** draft — not approved, do not implement

## Why

Cleared comments are deliberately not shown. The work list is a queue of things
needing attention, and safe comments are the large majority, so the team's
decision to keep them out stands.

The consequence is that **only false positives are ever visible**. A comment the
matcher wrongly clears disappears, and nobody finds out. With pattern matching
v0.1, a missed harmful comment is the more likely error and it is the invisible
one.

It also biases the training data. Corrections are the signal for the Khmer
model, and a correction can only be recorded on a comment somebody saw. If
cleared comments are never seen, the model can only ever learn from comments the
pattern matcher already caught. `surfaced_reason` exists on every verdict
precisely so that anyone training on the resulting corrections can correct for
selection bias; clearing without sampling bakes in the one bias that cannot be
corrected for, because the excluded population leaves no trace.

Partial protection already exists: `_looks_novel` routes Khmer text containing no
known vocabulary to `novel_language` rather than clearing it, so wholly
unfamiliar text does reach a person. The gap is text holding some known words but
no risk word — a comment naming a company alongside slang the lists do not have.

## Decision

Keep cleared comments out of the queue. Surface a small random sample of them,
recorded as its own `surfaced_reason`.

## Shape

**A new `surfaced_reason`: `cleared_sample`.** This mirrors
`institution_sample`, which already exists for the same purpose — deliberately
surfacing a population that routing would otherwise pass over.

**The sample is drawn once, at classification time.** Verdicts are append-only,
so a comment cannot be re-sampled later without rewriting one. Drawing at
ingest and recording the outcome is also what makes the sampling rate auditable
after the fact: the stored reason is the evidence of how the population was
selected.

**Sampled comments are fully actionable.** A moderator can correct the label —
that is the point — and can act on the comment if it turns out to be harmful.
Nothing about the sampled path is read-only.

**The UI must say why the comment is there.** A `cleared_sample` row is a
spot-check, not a threat. Labelling it as triage would teach moderators that the
queue contains harm when it does not, and they would learn to dismiss the row
without reading it.

## Also fix

`surfaced_reason=cleared` is advertised as a filter value in the API and offered
in the "Why surfaced" dropdown, but the work list excludes cleared rows before
any filter is applied, so choosing it always returns nothing. Now that hiding
cleared comments is a settled decision rather than an oversight, the dead option
should be removed rather than made to work.

## Open

**The rate.** 5% is a reasonable starting point for a busy Page. It is the wrong
shape for a small one: a shop receiving twenty comments a day would surface one
sampled comment every few days, which is too sparse to detect anything. A floor
per sync would fix that but over-samples tiny Pages, where a floor of one per
sync could mean sampling nearly everything. This needs a number chosen against
real volume rather than guessed, and the connected Page's actual traffic is the
place to get it.

**Whether the rate is per workspace.** A bank auditing carefully and a shop
wanting a quiet queue do not want the same rate. Per-workspace is the honest
answer and matches how keywords were just made per-workspace, but it is another
setting to build and explain.

**Whether sampled comments count as "needing review"** in the Overview totals.
Counting them inflates the workload figure with comments nobody has to act on;
excluding them means the queue holds rows the counts do not admit to.
