---
title: "Task Module — Round-2 Review: Grilling Log"
date: 2026-08-03
owner: "Sanchay"
status: "complete — folded into CHANGELOG as D67–D79"
tags: [rentok, tasks, review, decisions]
---

# Round-2 Review — Grilling Log

Adversarial review findings, worked through one at a time. Each row gets a decision.

> **Numbering warning.** This log numbers its own decisions **D64–D77**. A parallel session had
> already taken D64–D66 in the CHANGELOG, so when these were folded in they became **D67–D79**,
> and this log's D64 was dropped (the CHANGELOG's own D64 + D66 say the same thing better).
> **The CHANGELOG is the source of truth for D-numbers** — use the mapping below, not the
> numbers in this file.
>
> | Here | In CHANGELOG.md |
> |---|---|
> | D64 (standing rules dropped) | folded into the existing **D64** + **D66** |
> | D65 → D77 | **D67 → D79**, in order |
>
> This file is kept for the *argument* behind each call — what was rejected and why, including
> the three places the review was wrong. For what was decided, read the CHANGELOG.

## Order (dependency-sorted)

| # | Finding | Severity | Status | Decision |
|---|---|---|---|---|
| 1 | F6 standing rules + P2 — cut from this cycle? | HIGH | **decided** | Cut. Assign from the system-generated list instead. See D64 below. |
| 2 | The missing band rule: "anything Band B is wrong without is Band B" | — | **decided** | Rule added. See D65 below. |
| 3 | Brief says F1/F8 ship-blocking, bands say C | BLOCKER | **decided** | F8 → B, F1 stays C, Brief rewritten. See D66. |
| 4 | D13 makes D55 false on release day | HIGH | **decided** | Staff default to "see only my own", in V1, inside M2. See D67. |
| 5 | Pooled proof names people who were not there | BLOCKER | **decided** | Narrowed: no per-person count from pooled. Fan-out counts both ways. See D68. |
| 6 | Priya's D22 protection has no requirement | BLOCKER | **decided** | Shared list only, no head start. D22 reworded. See D69. |
| 7 | S2L monthly building audit cannot be expressed (route / repeatable section) | BLOCKER | **decided** | One task, one form. Problems as free text. See D70. |
| 8 | F54 repeat-failure join must ship with F2 | HIGH | closed by D65 | → Band B |
| 9 | F53 no-smartphone proxy → Band B | HIGH | closed by D65 | → Band B |
| 10 | F15a "couldn't do it" → Band B (+ fixed reason list) | HIGH | closed by D65 | → Band B (reason list still open) |
| 11 | F24c skip occurrence → Band B, + blackout dates | HIGH | closed by D65 | → Band B (blackout dates still open) |
| 12 | F33a reassign → Band B | HIGH | closed by D65 | → Band B |
| 13 | Time window, not just deadline (F18) | HIGH | **decided** | Window dropped. Send time + reminder model added. See D71. |
| 14 | F20 risks becoming the fourth list | HIGH | closed by D64 | F20 *is* the existing feed |
| 15 | Nothing decides what a task reveals about a tenant | HIGH | closed by D64 | Rule 5 — assigning does not hand over tenant details |
| 16 | Shared number: F40 "one message per person" | HIGH | **decided** | Not a real use case. No build. Three docs to correct. See D72. |
| 17 | Required photo + failing camera = dead end | HIGH | **decided** | Mostly closed by D65. F39 gains a cleanup line. See D73. |
| 18 | F7 My tasks voided by D26 export | MEDIUM | **decided** | D26 stands. Low F7 adoption expected, not a bug. |
| 19 | F13 Hindi → Band A | MEDIUM | **decided** | Translation deferred. Starter templates ship bilingual. See D74. |
| 20 | Instance-creation idempotency → P1 | MEDIUM | **dropped** | Manual duplicates are allowed by design. See D75. |
| 21 | F47 starter routines → Band C | MEDIUM | **decided** | → Band C, created unassigned. See D76. |
| 22 | F25a owner digest → Band C | MEDIUM | **decided** | → Band C. See D76. |
| 23 | Partial payment keeps the collection visit spawning | MEDIUM | closed by D64 | Died with F6 — no money rule exists |
| 24 | Device photo retention (F39) | MEDIUM | **decided** | Merged into D73 |

## Decisions

### D64 — Standing rules are dropped; system-generated tasks become assignable
**Decided 2026-08-03.**

Standing rules (F6) are not built this cycle, and the persisted room-empty flag (P2) is
dropped with them. The system-generated pending-task list is the only thing that watches
conditions — there is no second watcher.

A manager can hand a suitable system-generated entry to one or more people, which creates
real tasks with proof. Rules:

1. **Assign from the list behind a card, never the card itself.** She taps "5 tenants owe
   rent", sees the five rows, ticks three, assigns them. That creates three tasks, each
   locked to one tenant, each with its own proof. The card stays a count.
2. **Each registry entry is marked assignable or not**, one by one — not by category.
   "Tenants to install the app" is a visit; "WhatsApp balance low" is not.
3. **The assign path ships this cycle** against the cards live today (23 on the home feed
   + 10 in the backend block). **Growing the registry stays on issue #6249**, phased. New
   cards inherit the ability as they ship.
4. **A row shows when it already has a task out** — "assigned to Ravi" on the row, "3 of 5
   assigned" on the card — so she does not assign the same thing twice.
5. **Handing over a task does not hand over the tenant's details.** The assignee sees the
   place and the action ("Room 204 — collect rent"), not the amount or the document
   history, unless they already hold the permission for that entity.
6. **Dismissing a card never touches tasks created from it.**

**Side effect:** F20 ("one list for the manager") *is* the existing home feed with an assign
action, not a new screen. Finding #14 is resolved by this decision.

**Empty-room cleaning is not covered by this** — the registry has no "rooms vacant" card.
It is covered by **F3 pulled into this cycle**: a finished move-out creates the prep task
directly, using the same pattern as the existing complaint auto-raise. No poller, no flag.

**Rejected:** building four condition groups with their own scheduler. Three of the four
(dues, documents, complaints) already have a live watcher — a second one is a duplicate.
The fourth (room empty) reads the priciest state in the system and needs an invariant that
every tenancy write path must maintain forever; when it drifts, staff are sent to occupied
rooms and the manager switches the feature off. Also rejected: pulling the full registry
build-out into this cycle — that swaps a medium feature for a larger one.

**Requirements affected:** F6 cut · P2 cut · F48/F49/F50 kept, reframed as repeating-task
management (under D35 a rule *is* a repeating task) · F3 pulled from V1.1 into this cycle ·
F20 restated as the existing feed.

---

### D65 — The band rule: a thing Band B would lie without is Band B
**Decided 2026-08-03.**

Band C's definition ("the promise holds, but there are visible holes") had no room for items
that make a Band B feature *untrue*, so five of them were misfiled there. The bands gain one
rule:

> **If a Band B feature would produce a false record without it, or would damage another
> part of RentOk, it belongs in Band B.**

The test is tight on purpose. It is **not** "would this be better with it." It is "does the
record become false, or does another module break." F14 (comments), F24a (quick capture) and
F33b (bulk assign) all fail the test and stay in Band C — no comments is worse, not false.

**Five items move C → B:**

| Item | What it makes Band B lie about |
|---|---|
| **F53** — manager completes for someone with no smartphone | Ramu is permanently late; the founder's exception view (F22) shows a failure that never happened |
| **F15a** — "couldn't do it, here's why" | Blocked work and ignored work are identical in the record and in F21's numbers |
| **F24c** — skip a single occurrence | Festival week records the whole team as failing, every year, permanently |
| **F33a** — reassign, including by the holder | The guard going off at 10pm keeps open work that then goes overdue against him |
| **F54** — repeat failure joins the open complaint | *(the second kind)* one broken bulb becomes ten live complaints and the queue Priya relies on becomes unusable |

F54 moves for the second reason, not the first — it does not create a false record, it floods
the complaint module.

**Accepted cost:** Band B grows from ~30 to ~35 items. If B is over capacity for the cycle,
this pushes something else out — that trade is engineering's to surface, not a reason to
misfile the items.

**Still open inside these moves:** F15a's reason list (fixed options vs free text) and whether
F24c is accompanied by property-level blackout dates. Both are later rounds.

---

### D66 — The Brief stops calling the entity link ship-blocking; F8 moves to Band B
**Decided 2026-08-03.**

The Brief's *"What has to ship for the bet to hold"* named four things as ship-blocking. Two
of them (F8, F1) sat in Band C and one (F6) is now cut — so the Brief and the requirements
disagreed about what the cycle is for.

**The Brief was overclaiming, not the ranking.** What Priya misses on a Monday is the hour
spent handing out work and not knowing afterwards whether it happened — *tell* and *prove*,
which is what Band B already says. The entity link is what makes this **ours** rather than
MaintainX's. That is a true and important claim, and it is a different sentence from "this
has to ship."

**Three changes:**

1. **F8 moves C → B.** D45 already decided it (*"the 'every task ever on this room or tenant'
   history view gets built — it is what linking is for"*), so the ranking contradicted a
   locked decision. It is also the screen a dispute actually needs: the tenant says the room
   was filthy at move-in, Priya opens room 204 and shows nine months of dated proof. Without
   the screen the proof exists and nobody can find it — which protects nobody, which is the bet.
2. **F1 stays in Band C.** It touches one kind of thing, most tasks are not tied to a due, and
   it saves a few clicks. It reads as ship-blocking because it demos well, not because anyone
   is stuck.
3. **The Brief's ship-blocking paragraph is rewritten** to match Band B — tell people what to
   do, prove it happened, one place to see it. The entity link moves to a separate line about
   what makes us different from competitors.

**Accepted cost:** the Brief loses its crispest answer to *"why can't a competitor do this?"*
from the position where it read as urgent. It needs one strong sentence in its new home or the
differentiation quietly deflates.

---

### D67 — Staff default to "see only my own"; D13's blanket default is corrected
**Decided 2026-08-03. Ships in V1, inside M2, paired with M1.**

**This corrects D13.** D13 said every existing user keeps the access they have today. But the
task controller checks nothing today (Audit, Domain 3 — zero `checkAuthInDb` calls), so "today's
access" means *everything*. F26 would have shipped as a permission model with every flag open
for everybody — a data model, not a control — and nothing scheduled the tightening.

That also made **D55 false in production on release day.** D55 forbids staff seeing each
other's tasks and numbers, on the grounds that it builds "the leaderboard the bet forbids, by
the back door." Under D13's default that door is open from the first morning.

**The corrected default:**

> Anyone who cannot create or assign work defaults to **"see only my own."** Everyone else
> keeps today's access.

**Why it locks nobody out:** "see only my own" still shows a person every task assigned to
them — their entire job. The only thing removed is other people's work, which D55 already
decided they should not have.

**How the migration decides who is staff — corrected 2026-08-03 after a code check.** The
decision originally said the rule "defines itself from the flags already on
`team_member_property`." **That was wrong.** There is not one task-related flag anywhere in that
table's 94 columns — no `view_task`, no `create_task`, nothing (which is the Audit's Domain 3
zero, confirmed). So the migration must use an existing proxy.

**The proxy: anyone without `view_team` / `add_team` / `edit_team` defaults to "see only my
own."** Managing the team is the closest existing signal for "hands out work." **Rejected:**
`daily_ops` (means something broader than assigning work) and splitting by account role
(a senior manager who is not an admin would lose visibility on day one).

**Why V1 and not later:** this is a different value in the M2 migration F26 already ships —
not extra code. Deferring means running the migration twice, and the second run *removes*
access from people already using the module, which is strictly harder.

**Sequencing condition:** ships with **M1**. Room cleaning today gives every staff member one
shared link (D60); tightening before M1 would leave a cleaner with nothing. After M1 cleaning
is a pooled task assigned to the cleaning team, so it lands inside "my own." If M1 slips, this
slips with it.

**Safety valve already exists:** D49 releases to everyone but enables account by account, so a
bad first day is stopped instantly rather than rolled back.

**Accepted cost:** some support calls in week one from properties where a staff member was
relied on to check someone else's list. The answer to each is "give them the assign permission."

---

### D68 — Per-person numbers come from fan-out, never from pooled
**Decided 2026-08-03.**

The review opened this as "pooled proof names people who were not there." Sanchay narrowed it
correctly: **a missed pooled task carries no name at all** — nobody tapped, because nobody did
it. So "Sunita missed 6 rooms" is not computable and was never the risk. The reviewer's original
example was wrong.

**The real exposure is the completed side.** F21 promises "each person their own number." For a
pooled task that number can only be a count of self-taps, which fails twice: it is a ranking of
people built from self-declarations (the side door into what D15 and D22 forbid), and it is
permanently half the picture — a person who works hard and does not tap shows as idle with no way
to prove otherwise, while tapping becomes the rewarded behaviour.

**The rule, split by assignment mode (D8):**

| Mode | Person on the record | Counted per person? |
|---|---|---|
| **Fan-out / single assignee** | Yes, before the work happens | **Yes — completions and misses.** Ravi's round is Ravi's whether he does it or not. |
| **Pooled** | Only after completion, by self-tap | **No — neither direction.** |

- The self-stated name **stays visible on the individual task**. Priya needs "who do I ask about
  204?" That is context; it stops being context the moment it is totalled.
- Room-level and property-level numbers are unaffected, and are what Priya actually uses.

**Boundary held:** a per-person miss count on fan-out is Priya's working view ("Ravi's round
didn't happen, ask him"). It is not a ranking across people, and the founder's screen keeps naming
properties, not people — F22's own example is already property-level, so this stays consistent
with D22.

**Second gap this surfaced.** A missed pooled task has **no owner at all**. D60 justifies M1 with
"today no room has a recorded owner", but pooled only gives a room an owner *after* it is done —
the room that did not get cleaned still belongs to nobody. That is acceptable: the accountable
unit for a missed pooled task is **the cleaning team**. D60's sentence must say that instead of
implying every room gets a doer.

---

### D69 — The manager sees the same exceptions the owner sees; no head start
**Decided 2026-08-03. This reduces D22.**

D22 promised: *"Escalation reaches the owner only after she has had a fair chance to see it
first."* **No requirement implemented it.** F22 gave the founder his exception list, F25a gave him
a weekly digest, F18 escalated — and nothing gave Priya either a head start or sight of what the
owner is being shown about her property. She is the named top-3 adoption risk and every guard in
the module pointed down at staff.

**What ships:** the manager sees **the same exception list about her property that the owner sees
about it** — same query, filtered to her property, on her screen. She is never blindsided in a
call, and she always knows exactly what he is looking at. One screen, reusing what F22 already
builds.

**What does not ship:** the head start. There is no rule that the owner's thresholds are later
than hers, and no hold window. He may learn something at the same moment she does.

**Therefore D22 is reworded** to what is actually built. Leaving the original sentence in the
source of truth would repeat the exact problem D66 just fixed — a doc promising something the
build does not do. The protection is real but smaller than the original claim: *no surprises*,
not *first look*.

**Rejected:** an urgency override (certain critical failures skip to the owner) — it needs a
notion of critical checks that does not exist, and once there is an override the manager can
never be sure which things bypass her.

---

### D70 — A property-wide audit is one task and one form; problems are free text
**Decided 2026-08-03.**

**The reviewer was wrong and Sanchay corrected it.** The review proposed a repeatable checklist
block plus by-floor scope so a monthly building audit would not fan out into 200 tasks. That was
over-built. A monthly audit is **one walk, one form, one monthly report** — the shape
S2L's own supervisor audit already takes, so it is observed behaviour, not a guess. It needs no new
question type, no new scope branch, and no engineering. Every question type already exists.

**The shape:** one property-wide task, monthly. "All rooms clean? / Lift working? / Generator
checked?" plus a free-text question listing any problems found, with photos.

**Accepted, with the risk recorded:** problems are typed as free text, so a human still reads them
and creates the tickets by hand. **This leaves S2L's stated top ask partly unmet** — their example
was *"auditor marks 'Room 101 light broken', a human then has to create the ticket and assign it
to Pankaj the electrician."* On a property-wide audit that human stays in the loop every month.

**Rejected:** replacing the free-text box with an "add a problem" picker (where / what / photo,
pressed once per problem), which would have made each problem its own ticket via F2 and made
"problems by room" countable in F21. It was argued as *less* typing for the auditor and one more
question type inside F29, which this cycle already touches. Not taken.

**Partial mitigation that already exists:** per-room checklists are unaffected. Daily room cleaning
is one task per room, so a failed item there already knows its room and raises the ticket properly
through F31 → F2. Only the property-wide audit loses the room.

**Open gap left standing:** F2 assumes a complaint's location comes from the task's own location.
Nothing lets a problem name a place the task does not itself cover. Revisit if S2L complains about
re-typing.

**Also dropped from the review's proposal:** by-floor scope was recommended as a nearly-free win
(it is one of the three named orphans — fully implemented, unreachable because
`createTaskSchedule` does not accept scope fields). It is not needed for the audit. Still worth
switching on for other work.

---

### D71 — Reminder model: four moments, all batched
**Decided 2026-08-03.**

The review opened this as "a task has a deadline, but real work has a window" — night rounds,
morning cleaning, a 9–11 inspection. **That half was dropped.** Setting the due time at the *end*
of the acceptable window solves it with no build; the only thing a real window adds is "not
before X", which almost nobody needs and whose two real cases (meter readings after the billing
date, room prep after checkout) are deferred anyway. **Guidance, not a field:** a due time means
the end of the acceptable window, not the ideal moment. The starter templates ship set up that way.

**What the round actually surfaced** — raised by Sanchay, not in the review — is that nothing said
*when* a reminder arrives relative to the due time. A message at the due time is a notification of
failure. D24 had a daily summary, immediate messages, and escalation, with nothing in between.

**The model:**

| Situation | What happens |
|---|---|
| Scheduled work due today | One morning message at the **property's send time**, carrying all links |
| Ad-hoc work | A message the moment it is created, whatever the due date |
| Has a due time, not done | **One nudge an hour before** — a count and one link, batched |
| Has a date but no time, not done | **One nudge at 6pm**, fixed — same shape |
| Self-task (F7) | Fires at the time the person set. No batch, no nudge, no escalation. |
| Late | Escalation, rate-capped, daytime (unchanged, D24) |

**Rules that hold the model together:**

1. **The send time is per property, not per person.** Shift differences are handled by the manager
   setting a due time on the task — there is no roster data (D34) and the system should not infer one.
2. **Everything batches.** 200 rooms due at 11am is one nudge per person, not 200. Without this the
   WhatsApp number is throttled at the first large property.
3. **The nudge carries a count and one link, never the task links.** The morning message is the
   delivery; the nudge is a poke. Repeating the links makes it a second morning message and people
   stop reading both.
4. **6pm is a constant, not a setting.** One more property setting is one more thing set wrong. It
   works for day staff and for night staff starting at 10pm. Change the constant if a real property
   disproves it.
5. **Four message types is the ceiling.** D17 made WhatsApp the only channel with no fallback.
   Anything added later replaces one of these rather than joining them.

**Rejected:** 11pm for the no-time nudge (nobody does property work at that hour, it reaches people
asleep, and it breaks D24's own daytime principle — it is a failure notice with an hour left, not a
reminder). Also rejected: anchoring reminders to a task's start as well as its due — recurring work
already appears in the morning message on both its start day and its due day, so a separate start
anchor adds a fifth message and no new information. Also rejected: per-checklist or per-property
nudge lead times — one fixed hour until something proves it wrong.

---

### D72 — Staff do not share phones; the persona claim is wrong and three docs must be corrected
**Decided 2026-08-03.**

The review raised that F40/D24 promise "one message per person per day" while WhatsApp delivers to
a *number* — so at a property where several staff share one handset, fan-out work arrives as
several unlabelled messages in one chat and the volume doubles.

**Sanchay's correction: the shared-phone use case does not exist in RentOk's customer base.** Staff
have their own numbers. **No build is needed** — no name-labelling, no grouping by number, no
kiosk mode.

**This invalidates a claim carried in three docs**, which must be corrected so future readers do not
design around a constraint that is not real:

| Doc | The wrong line |
|---|---|
| **Task Module Brief.md** | persona: *"Share a cheap Android phone, often one between several, on a weak connection."* |
| **Feature Gap Audit** | cross-cutting: *"Shared devices — kiosk/quick-switch is the default deployment pattern, not a corner case."* |
| **review-findings.md** | opens with shared-device as *"the one thread behind half of it"*; strategic call #1 |

**The correction:** the weak connection is real and everything built for it stands — offline partial
save, photo compression, the 3-second cold-load gate. **The shared phone is not.** Kiosk and
quick-switch stay in the v2 backlog as a **watch item**, not a known gap: revisit only if the
requirement comes from real users.

---

### D73 — Photos are deleted from the phone once uploaded
**Decided 2026-08-03. Merges review findings #17 and #24.**

F46/D52 make photo questions camera-only with no exception — correct and unchanged; one gallery
upload would make every photo in the system worthless. F30/D63 make a required item unanswerable-
skippable. Together, a camera that will not open means a required-photo checklist cannot be
submitted by any route.

**The most common cause of a cheap Android camera failing is a full phone**, and camera captures
land in the device gallery by default. 200 rooms × one photo × daily fills a handset in weeks.

**Already fixed by D65:** F15a ("couldn't do it, here's why") and F53 (manager completes on behalf)
both moved to Band B, so two escape hatches now ship.

**What this decision adds — one line in F39:** once a photo has uploaded, **the local copy is
deleted**. The photo lives in RentOk, which is where the proof belongs. This removes the main cause
of a dead camera and stops a work phone filling with months of room pictures.

**Rejected:** a gallery fallback when the camera fails — the hole becomes permanent the moment it
exists, and D52 already settled it. Also rejected: keeping the local copy for a few days to allow
retry from the phone — slower to free space, and F12's partial save already holds unsent work.

**Rough edge accepted, not built for:** F15a is task-level. If the work *was* done and only the
camera failed, "couldn't do it" records that the work did not happen. Wrong shape for that failure,
narrow enough not to build for.

---

### D74 — Translation is deferred; managers write in their own script; starter templates ship bilingual
**Decided 2026-08-03. This replaces F13 as written.**

The review argued F13 (Hindi + one regional language) belonged in **Band A**, not B — an English
checklist answered by a Hindi reader produces answers to questions the person did not understand,
which is a false record, which is Band A's own test. F13's own text also said *"this is not a
fast-follow."*

**Sanchay's call: do not translate. The manager writes the task and her own checklists in her own
script — Devanagari or any other vernacular. Translation moves out of this cycle entirely.**

This extends D32 (a manager's typed text is shown as typed) from ad-hoc tasks to all
operator-authored content. It needs no engineering, and it covers most of what a cleaner actually
reads.

**Two consequences, accepted:**

1. **The app's own words stay English** — Submit, Photo required, Overdue, Approve. Friction rather
   than a wall; staff learn a handful of buttons by position. And if the rest of the RentOk Manager
   app is English today, this module should not be the first one translated.
2. **Other regional languages defer with it.**

**The one thing kept: RentOk's starter templates ship in Hindi as well as English.**
F9 exists to remove the blank box and F47 has new properties start with routines already running.
English-only templates would force a Hindi-first manager to rewrite every one before her staff could
use them — the blank box with extra steps, in the feature built to prevent it. Writing the same
checklists twice is **content work on templates being authored anyway**, not engineering, and it is
where F9's value sits.

---

### D75 — Duplicate tasks on the same thing are allowed
**Decided 2026-08-03.**

The review proposed a uniqueness rule on task creation (one schedule + one period + one target = one
task) to stop a scheduler retry or redeploy producing a phantom "not done" record.

**Rejected.** Duplicates created by a person are legitimate and must stay possible — a manager or
team member may deliberately create two tasks on the same room on the same day, and people close
what they do not need. **Written down so nobody adds a uniqueness constraint later and breaks it.**

---

### D76 — Starter routines and the owner's digest move to Band C; routines are created unassigned
**Decided 2026-08-03.**

**F47 (new properties start with routines running) and F25a (weekly WhatsApp digest to the owner)
both move Band B → Band C.** Neither is the promise. F22 gives the owner his exception list and D69
gives Priya the same view of her property; the weekly digest is convenience on a screen that
already exists.

**How a new property starts, refined through two rounds.** The review's objection to F47 was that a
brand-new property has no staff, so routines fire into nobody and the customer's first week is a
list of failures. Sanchay first proposed routing to the admin (the only member at cold start). That
fixes "goes to nobody" but not the real harm — a 60-bed property switching on daily cleaning gives
the owner 60 tasks a day while he is still hiring, and the property accumulates a failure history
before anyone existed to succeed, which then poisons F21 and F22 the moment he does hire.

**The landed answer:** routines are **created, enabled, and unassigned**. Nothing fires until the
admin assigns someone, which he has to do anyway. He opens the app and sees his property already
set up — F47's actual value ("no blank box") — with no failure history accruing against nobody.

**Corrected 2026-08-03 after a code check — this needs a scheduler change, not zero work.** The
decision originally claimed unassigned schedules already produce nothing. **That was wrong.** The
real behaviour in `taskScheduler.ts` is:

```
if (schedule has team members AND system_purpose !== 'room_cleaning')
    → one task per member
else
    → ONE task with team_member_id = undefined
```

So an unassigned routine **does** run — one unassigned task per room, every day, with a live
access token. A new 60-bed property would get 60 unassigned tasks on day one and 180 by day three,
which is precisely the harm this decision exists to prevent.

**The fix: the scheduler skips any schedule with no assignees.** One condition. **Sequencing
consequence:** `room_cleaning` is *deliberately* excluded from per-member fan-out today, so it
always creates unassigned tasks — this is D60's "everyone gets one shared link" visible in code.
The skip rule would therefore stop room cleaning entirely, so **M1 must land first**, which D67
already requires for its own reasons. **Rejected:** adding a paused state (more to build, and
leaves the underlying scheduler behaviour wrong for everyone else).

**One guard, because "unassigned" is silent.** Fine on a new property; dangerous on a running one.
Ravi quits, Priya removes him from the cleaning routine, he was the last person on it, and cleaning
stops silently for a week. F51 covers the person leaving; it does not cover the routine falling to
zero people. So, inside F48:

- A routine with nobody assigned displays **"Not running — nobody assigned"**
- Removing the last person warns: *"This will stop the routine. Continue?"*

---

### D77 — "Couldn't do it" is free text; no holiday list
**Decided 2026-08-03. Closes the two items left open by D65.**

**F15a's reason is free text.** The person types why they could not do it. Zero build. **Rejected:**
a short pick-list (*not home · refused · will do later · wrong person · no access*), which would
have been countable — Priya seeing "not home ×8 this month" and switching to evening visits.
Consequence accepted: the manager reads individual excuses and never learns the pattern. Consistent
with D70, which chose free text for audit problems on the same trade.

**No property-level holiday list.** F24c (skip a single occurrence) is in Band B, so a festival is a
few taps. A holiday list is a new screen and a new setting for something that happens a handful of
times a year. **The review raised it and then withdrew it.** Revisit only if a property running many
routines complains.

---

## Noted, not fixed

**F22's shape still works against D22's spirit (from D69).** "Sunshine PG: 6 rooms not cleaned in 3
days", newest first, across eight properties, read on a Sunday — the owner is counting how often
each name appears. That is a ranking arrived at by inference rather than by design. It is the honest
cost of giving the owner anything at all, and there is no cheap fix.

**F7 adoption (from finding #18).** D26 stands: "My tasks" appears in the owner's export and is
never called private. Consequence accepted — the manager most likely to need it is the one most
likely to keep using paper. Treat low F7 adoption as expected, not as a bug.

**F2 cannot name a place outside its own task (from D70).** A problem found on a property-wide audit
raises a complaint against the property, not the room. Revisit if S2L complains about re-typing.

---

## Settled later — what the moat is (D80, D81)

**Settled 2026-08-04 as [D80](../CHANGELOG.md). The Brief is rewritten.**

> **The answer:** a moat is not decided by looking at competitors — it is decided by what the
> product does for the people it is built for. The moat is **canonical sentence 8**: *a property
> runs on a system, not on one person's memory.* The routines accumulate Priya's operating
> knowledge out of her head, she builds them herself, and at PG churn rates the payoff lands
> every month. **F1 stays Band C.** F9/F47 are the on-ramp; F24c/F48/F49/F50 are moat defence;
> F22/F25a are sales. The no-fines promise is the precondition, not a parallel value.

The two wrong answers below are kept because both were plausible and both were discarded for
reasons worth remembering.

The doc-handoff review found that D68's line *"F1 is moot"* is wrong — F1 (*a task shows the
linked thing's live state*) **is** the requirement that implements D65. It sits in **Band C**
while the Brief presents it as one of the two things that make this ours. So either F1 moves up,
or the Brief's differentiator claim changes. That question opened a bigger one.

**First answer, and it was wrong.** The reviewer proposed the moat is F58 (an alert becomes work)
plus F3. Sanchay's objection: **the alert lives on the home screen, not in the Task module.** If
that is the moat, the moat is not in the module being rebuilt.

**Second answer, also discarded.** Test each claim against MaintainX:

| Claim | Can a competitor do it? |
|---|---|
| A task shows the linked thing's live state (F1) | Not today, but it is a display — an integration approximates it |
| Every task ever done to room 204 (F8) | **Yes** — asset history is core to MaintainX |
| Failed check → routed ticket (F2) | **Yes** — their strongest area. We are catching up, not leading |
| No fines, no scorecard (the bet) | Yes, if they chose. Positioning, copyable in a sprint |

On features we lose. The operator's real question is not "which task tool is better" but **"do I
run a second system?"** — a second staff directory kept current at PG churn rates, 200 rooms
re-created as assets, a second WhatsApp number, a second per-seat bill, a second login. We
already hold all of it.

So the proposed framing: **this cycle's moat is consolidation, not capability. We win by being
adequate and already there.** That makes the ship bar *"good enough that nobody goes looking"* —
which is Band A + B as they stand.

**The one real capability moat is not built yet:** producing work nobody thought to assign. Today
still needs Priya to notice an alert. The moat version is the property's own events creating the
work — a move-out finishes and the prep task exists, without anyone looking. **This cycle ships
exactly one of those (F3) and the seam it runs on.** Honest to say it is one event old.

**Two smaller things that are genuinely ours today:** the proof attaches to money (a photo of
Room 204 sits beside a deposit deduction, a rent dispute and a tenant complaint on the same room
— F8's real value is not that history exists but that it is admissible where the money argument
happens), and the complaint queue is shared with the tenant.

**Brief change proposed by this discarded answer, and NOT applied:** it suggested dropping *"None of them can do the things we can"* — it is not
true on features and invites the comparison we lose. Replace with, in order: (1) you will not run
two systems; (2) the proof lands where the money argument happens; (3) the work is starting to
appear on its own.

**Why the second answer was discarded too:** consolidation is a real reason nobody buys a second
tool, but it is still a competitor-shaped argument — it describes why they do not leave, not what
the product does for them. The third answer (D80) is the product's own: the routines accumulate
out of one person's head, which is what this business, with its churn, actually needs.

**Settled:** F1 stays Band C.
