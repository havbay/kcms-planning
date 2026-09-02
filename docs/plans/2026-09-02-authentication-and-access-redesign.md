# Authentication And Access Redesign

**Status:** draft — not approved, do not implement

Held until the team finishes the work currently in flight. Recorded now so the
decisions are not re-litigated from memory, and so nobody starts the invitation
registration endpoint that this supersedes.

## Why

Two problems in the current model, one of them blocking.

**Team invitations are unusable.** An owner creates an invitation link and sends
it. The invited person opens `/join/<token>`, sees the workspace, and is offered
only "Sign in to accept this invitation". They have no account, and there is no
way to create one: `POST /api/v1/auth/signup` returns 404 in production because
`public_signup_enabled` is false. The Team feature cannot be used for the thing
it exists to do.

**Reviewed pilot access does not scale.** A visitor requests access, a Platform
Administrator approves, and the owner creates credentials through a one-time
setup link. That gave control during the pilot and cost nothing while there were
no customers. It is a manual step on the growth path.

## Reference

ChatKH (chatkh.com), a Cambodian Facebook/Instagram automation service in an
adjacent market, resolves both differently:

- Owners sign up with **Facebook or Google only** — no email/password
  registration, a Terms checkbox, nothing else.
- Team members have **no signup path at all**. The owner creates the account
  inside the product and hands over the credentials.
- Team members sign in on a **separate screen** with email and password, without
  social login, registration, or password reset.

The second point is the one worth taking: it removes the invitation dead-end by
deleting the need for an invited person to register.

## Decisions

### 1. Owners sign up with Facebook, Google, or email and password

Social signup, with the email and password route kept alongside it.

Facebook signup is a particularly good fit: every customer already has Facebook,
and KCMS needs their Facebook authorization anyway to reach the Page. One
authorization can serve both.

**The email and password route is not a nicety.** The Meta app is in Development
mode, where Facebook Login only works for people holding a role on the app.
Facebook-only signup would therefore admit nobody outside the app roles until
Meta App Review passes — a harder gate than the pilot review it replaces. App
Review additionally requires a reachable privacy policy, and `/privacy` currently
returns 404.

Rejected: Facebook and Google only. Correct destination, unreachable until the
app is Live.

### 2. Owners create team member accounts inside the product

The owner enters a name, email and password on the Team screen; the account
exists immediately with membership of that workspace.

No invited person ever registers, so the dead-end cannot occur. This supersedes
the drafted `POST /api/v1/team/invitations/{token}/registration`, which should
not be built.

Known cost, accepted: the owner handles a colleague's initial password. A first
sign-in password change is the natural follow-up and is out of scope here.

### 3. One sign-in screen for everyone

KCMS already models a team member as a user with a `membership` row whose role
is `member`. One form works, and the role comes from that membership.

Rejected: a separate "Login as Team Member" screen. It only pays for itself if
team members become sub-accounts scoped to an owner rather than real users, which
is a much larger change to the identity model than the problem justifies.

## What this supersedes

**D-025** disabled public self-signup and routed every account through pilot
review. Decision 1 is the opposite of that, and is a deliberate product change
rather than drift. It needs its own decision record when this is approved, in the
way D-026 replaced the demo-only Page-connection exception. Recording it matters:
an unexplained second account-creation path reads as a mistake to whoever finds
it next.

Also superseded: the shareable invitation link and `/join/<token>`. Removing them
closes a risk noted while drafting the registration endpoint — a leaked link
would have granted account creation plus workspace access with no review at all.

## Sequencing

1. Email and password signup for owners, so a real customer can sign up before
   App Review.
2. Owner-created team member accounts. Unblocks the Team feature.
3. Facebook and Google signup — after the Meta app is Live, which needs App
   Review, which needs `/privacy` to exist.

Steps 1 and 2 are independent of Meta and can proceed at any time. Step 3 cannot
be finished before App Review, whatever else is ready.

## Open, not decided here

- Whether an owner-created member must change the password at first sign-in.
- Whether existing pilot-request records and the setup-invitation flow are
  retired or kept for a transition.
- Password reset. There is no email infrastructure, and adding one puts
  deliverability on the critical path of sign-in.
