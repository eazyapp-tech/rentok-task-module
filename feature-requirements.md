---
title: "Task Module — Feature Requirements"
date: 2026-08-04
version: "2.3"
owner: "Sanchay"
status: "current"
tags: [rentok, tasks, requirements]
---

# Task Module — Feature Requirements

Every requirement, with **what it is** and **what the operator loses without it**. These F-numbers are the ones every other doc refers to. The [CHANGELOG](CHANGELOG.md) holds the decisions (D#) behind each one, and the PRD and workflow specs describe them in full.

## How to read this

**The bands are a cut order, ranked by what the user misses most — not by build cost.** Engineering owns build cost and decides what fits V1; this document says what is needed, why, and in what order it should survive a cut.

| Band | Meaning |
|---|---|
| **A — Foundation** | The module is unsafe, broken, or dishonest without it. A record nobody can trust is worse than no record. |
| **B — The promise** | Without it the module does not do what we said it does: tell people what to do, prove it happened, let each level see. |
| **C — Completeness** | The promise holds, but there are visible holes. |
| **Later** | Deliberately deferred, with a named home. |

**The rule that decides between B and C (D67):** *if a Band B feature would produce a false record without it, or would damage another part of RentOk, it belongs in Band B.* The test is tight — not "would this be better with it", but "does the record become false, or does another module break." F14, F24a and F33b all fail it and stay in C.

**Finding an F-number:** **A** — F26, F36, F37, F38, F41, F44, F45, F46 · **B** — F2, F3, F4, F5, F7, F8, F9, F10, F11, F12, F13, F15a, F16, F17, F18, F19, F20, F21, F22, F24c, F29, F30, F31, F32, F33a, F39, F40, F48, F49, F50, F51, F53, F54, F57, F58 · **C** — F1, F14, F15b, F24a, F24b, F25a, F25b, F27, F33b, F43, F47, F52, F59 · **Later** — F3b, F6, F28, F34, F35, F42

**Prerequisites** and **migrations** are listed separately. They are sequencing facts, not priorities.

---

## Prerequisites — nothing below works without these

**P0 — The scheduler must skip any routine with nobody assigned.**
Today it does the opposite: with no assignees it creates **one task with no owner** (`taskScheduler.ts` — per-member fan-out is gated on members existing *and* `system_purpose !== 'room_cleaning'`). So a new property with starter routines fires tasks at nobody from day one (D78). **Runs with M1**, because room cleaning today deliberately creates unassigned tasks — skipping before M1 would stop cleaning dead.
*Without it:* F47 ships and a brand-new customer's first week is a list of failures against a property with no staff, and every unassigned routine writes false "not done" records.

**P1 — A reliable, authenticated scheduler that fires recurring work.**
There is no scheduled job anywhere in the codebase; the trigger endpoint is open and depends on an unidentified external caller ([D40](CHANGELOG.md), backend issue #6363).
*Without it:* recurring work silently never appears, and nobody finds out until a manager asks why the cleaning list is empty. Every recurring requirement below sits on this.

*(A second prerequisite — storing whether a room is empty — was dropped along with standing rules. See D64.)*

---

## Band A — Foundation

Nothing here is a feature the operator asks for. All of it is what makes the record worth having.

**F26 — Access control across the module. Managers keep today's access; staff default to seeing only their own.**
Five permissions: see tasks · see only my own · create and assign (includes editing) · review · archive (D55, D56, D57). **Anyone without `view_team` / `add_team` / `edit_team` defaults to "see only my own"; everyone else keeps today's access (D69, correcting D13).** Ships in M2, paired with M1.
*Without it:* today the module checks no permissions at all — any user can see and act on any task in any property. And because "today's access" means *everything*, a straight keep-what-you-have migration would ship a permission model with every flag open, leaving D55's no-leaderboard promise false in production from the first morning.

**F41 — The runner proves who is submitting; links expire.**
The person's identity comes from the task record, not from the request body, and a task link stops working when its window closes (D54). No login — that friction would break the cold-load gate.
*Without it:* the submitter's identity is whatever the caller types. Anyone who can reach the endpoint can file work as anyone, so every promise about proof is empty and the record protects nobody.

**F46 — Photo questions open the camera, never the gallery.**
Always, with no per-checklist setting (D52).
*Without it:* a photo can be an old picture, or someone else's. One gallery upload makes every photo in the system worthless as evidence — including the honest ones.

**F36 — Every submission is validated on the server against the checklist it belongs to.**
*Without it:* answers are stored unchecked, so a "completed" record may not correspond to the questions that were asked. The evidence a dispute rests on is unverified.

**F44 — A checklist with open tasks cannot be edited; you save it as a new one instead.**
*Without it:* editing a live checklist invalidates work already in progress — a person's answers are rejected or judged against questions they never saw, and their effort is lost. (Version-pinning every task would also solve this; blocking the edit is the cheaper fix. Engineering to confirm which.)

**F37 — An edit log on every task, and a lock after submission.**
Who changed what and when; a submitted record cannot be quietly altered (D44).
*Without it:* proof can be edited after the fact, which means it proves nothing — and the person it was supposed to protect has no defense.

**F38 — Archive and restore instead of deleting.**
Tasks, templates and rules (D38).
*Without it:* an accidental delete destroys work records permanently, and there is no way back.

**F45 — The server decides what is late, and several missed reminders arrive as one message.**
Never the phone's clock (D44, D23).
*Without it:* a phone with a wrong clock creates false overdue records, and two days offline produces a burst of stale reminders that trains people to mute the channel.

---

## Band B — The promise

### Telling people what to do

**F4 — Create a one-off task at any time: assign it, set a due date and time.**
*Without it:* nothing ad-hoc can be given to anyone. The manager is back on WhatsApp for anything unplanned.

**F5 — Create a recurring task on a real cadence.**
Daily, weekly, monthly, chosen weekdays (Mon/Wed/Fri), and a chosen date each month (D16).
*Without it:* common-area cleaning on alternate days and meter readings on the 1st cannot be expressed, so the manager creates three separate weekly tasks by hand — the exact work we are removing.

**F10 — Scope a task to the property, a floor, specific rooms, or areas.**
Picked the way a complaint's location is picked, but multi-select with an "all rooms" option (D20).
*Without it:* every task a manager creates covers the whole property. "Clean each room" is impossible outside the one hardcoded cleaning button, and per-room accountability cannot exist.

*(Operator-managed areas — lobby, lift, stairs — moved to V1.1. In V1 a shared space is named in the task and grouped with a tag (F43); what we give up is per-area history, which nobody is asking for yet.)*

**F16 — Assign to several people two ways: pooled or one-each.**
Pooled means any one person completes it and it closes for all; one-each gives everyone their own copy. Either way the system records who did what (D8, D19).
*Without it:* "someone clean the lobby" and "each guard does their own round" become the same thing — so either three people do the same job, or nobody is responsible for it.

**F58 — Turn an alert into work: one task per item.**
The pending-task alerts are already live — they stay on screen, update in real time, and respect who can see them. This adds one action: *create work from this*. Twelve overdue dues become **twelve tasks**, each linked to its own due, all assigned in one action, and she can pick a subset from the filtered list (D66).
*Without it:* she taps the alert, sees the twelve, then goes to the task module and re-selects those same twelve by hand. The alert can show a problem but can never become work with an owner and a record — which is the gap between the two halves of the product.

**F9 — A checklist library to start from, which the operator can change.**
RentOk recommends a starter set based on property type and which modules are on; she can copy any template and edit its questions, or write her own (D58, D59).
*Without it:* setting up the module means facing a blank box, which is where a busy manager stops.

**F48 — Manage repeating tasks: list, edit, pause, resume, archive, and see what each has produced.**
Editing affects future work only; work already created finishes and keeps its proof (D41). Under D35 a rule *is* a repeating task, so this is needed for F5 whether or not conditions ever exist. **A routine with nobody assigned shows "Not running — nobody assigned", and removing the last person warns first (D78).**
*Without it:* she can switch a routine on but never check it, correct it, or stop it — and a routine that quietly falls to zero people stops for a week before anyone notices.

**F49 — Show how many rooms or people a routine will affect before it is switched on.**
(D37)
*Without it:* she switches on an all-rooms daily routine blind, floods her staff with 200 tasks a day, and stops trusting the feature.

**F50 — A routine that only produces ignored work pauses itself.**
Five firings with no completions, or nothing completed in 14 days; nothing is deleted and she is told why (D33).
*Without it:* one forgotten routine quietly buries the list and the notification channel.

**F3 — A finished move-out creates the room-prep task.**
Pulled from V1.1 (D64 as amended). The move-out lock path already exists and already raises complaints, so this is a call added to code that is already there, not new machinery — and it needs no stored occupancy flag.
*Without it:* vacant-room readiness has no home at all. A room falls empty, nobody is reminded to make it show-ready, and the 7–10 day window with real money in it depends on the manager remembering.

**F7 — Keep tasks for yourself, with reminders.**
Called "My tasks"; anyone can set their own routines, or routines for the people they lead (D9, D26).
*Without it:* the manager's own follow-ups stay on paper and WhatsApp, and the module only holds work she gives to others — not the work she owes herself.

**F18 — Due dates, overdue, reminders and escalation.**
Every period is its own task: an unfinished one closes as *not done* when the next fires, a late submission is still accepted and marked done-late, and partial work is preserved (D23). **A due time means the end of the acceptable window, not the ideal moment (D73)** — a night round due at 6am, not 2am — so window work is not marked late by an arbitrary minute. Starter templates ship set up that way.
*Without it:* nothing is ever late, nothing chases itself, and a missed day silently disappears — so the record cannot show whether the work actually happened.

**F40 — Notifications: four moments, all batched.**
(D24, D17, D73.)

| When | What arrives |
|---|---|
| Scheduled work due today | One morning message at the property's send time, carrying all links |
| Ad-hoc or rejected work | A message the moment it happens |
| Has a due time, not done | One nudge an hour before — a count and one link |
| Has a date but no time, not done | One nudge at 6pm |
| Self-task (F7) | At the time the person set. No batch, no nudge, no escalation |
| Late | Escalation, rate-capped, daytime |
Two rules hold it together: **the send time is per property, not per person** (there is no roster data — shift differences are handled by the due time the manager sets, D34), and **everything batches** — 200 rooms due at 11am is one nudge, not 200.
*Without it:* at 200 rooms a daily routine sends thousands of messages, WhatsApp throttles or blocks the number, and staff mute the only channel that reaches them. And a message that arrives at the due time is a notice of failure, not a reminder.

### Proving it happened

**F11 — Do the task in the runner, with proof.**
Time, photo, signature, and location checked against the property — recorded and flagged, never blocking (D52).
*Without it:* there is no evidence the work happened, which is the whole product. Blocking on location instead would stop real work indoors, and staff would start avoiding the app.

**F12 — Partial work survives a tab close, a network drop, and a phone restart.**
Held on the phone this version (D12).
*Without it:* a cleaner half-way through a ten-item checklist loses everything when the signal drops — and does not start again.

**F39 — Photos are compressed on the phone before upload, and the local copy is deleted after it.**
(D75.) The photo lives in RentOk, which is where the proof belongs.
*Without it:* proof cannot be submitted at all on a weak connection, which makes the 2G requirement meaningless — and a work phone fills with months of room pictures until the camera stops opening, which on a required-photo checklist blocks the submission entirely.

**F13 — RentOk's starter templates ship in Hindi as well as English; the operator writes in her own script.**
(D76, replacing the earlier "translate the app" requirement.) The manager writes tasks and her own checklists in whatever script she uses — this is D32 extended to all operator-authored content, and it needs no engineering. Translating the app's own words, and other regional languages, are deferred.
*Without it:* English-only starter templates force a Hindi-first manager to rewrite every one before her staff can use them — the blank box with extra steps, in the feature built to prevent it.

**F17 — Every staff member sees their own tasks and their own record.**
Only their own — not other people's tasks or completion (D55).
*Without it:* the proof belongs to the owner rather than the person who collected it, and the bet the module rests on is not actually built.

**F29 — The question types a real inspection needs.**
Rating, pass/fail/not-applicable, multi-select, several photos, voice note, date and time, a measurement with a unit, and a non-input instruction block.
*Without it:* an inspection cannot record "not applicable", a quality score, or a meter reading — so the checklists that matter most cannot be written.
*Note for the build:* the builder already supports a **select with options**, so pass/fail/not-applicable is a three-option select and a 1–5 rating is a five-option select — configuration, not new code. Genuinely new: several photos on one item, and the instruction block.

**F30 — Per-item settings.**
Mark an item required (and a required item must be answered before submitting — D63), require a photo on it, attach a reference picture, group items into sections, add a note.
*Without it:* "required" means nothing, so the one photo a dispute needs is the one that gets skipped.

**F31 — A single item can carry a problem, and a problem does not fail the task.**
Nine items fine and one fault reported means the task is complete with a problem recorded (D62); the failed item is what raises the complaint.
*Without it:* the person who reports a fault gets a failed task on their own record. So they stop reporting faults and mark everything fine — and we lose the fault and their trust together.

**F2 — A reported problem hands over a complaint, pre-filled, raised with one tap.**
It carries the issue and asset details, links to the task, runs on its own status, and uses the complaint module's existing category routing so it reaches the right person automatically (D38, D18, D4).
*Without it:* a fault found during an inspection dies in a form. This is the single thing S2L is waiting for, and the reason an audit today needs a person to re-type findings into a ticket.
*Partly, not wholly:* on a property-wide audit the problems are free text (D72), so a person still creates those tickets by hand. F2 removes the re-typing only where the task already knows its room.

### Letting each level see

**F20 — One list for the manager: all three sources, filtered by category, sorted by due date then priority.**
System-raised, assigned, and her own (D5, D31).
*Without it:* she checks three places, so she checks none of them, and the module becomes another tab.

**F57 — System-raised tasks carry the same categories as everything else.**
(D45)
*Without it:* the single filter in F20 breaks for one of its three sources on day one.

**F32 — A task carries a category, a priority, a description, and — if recurring — an optional end date.**
*Without it:* F20's filter and sort have nothing to work with.

**F19 — Review: approve, reject with a reason, or send back for rework.**
Set per checklist, off by default (D53).
*Without it:* nobody checks anything, so submission and completion mean the same thing. Turned on everywhere instead, a manager faces 200 approvals a day and bulk-approves without looking, which is worse.

**F21 — The first insight cut: completion, on-time rate, problems by room, week-over-week.**
Shows the state of the *work*, and each person their own number — never a ranking of people (D51, D22). **Per-person numbers come from fan-out and single-assignee work only, never from pooled (D70)** — a pooled task's name is a self-tap, so counting it would build a ranking out of self-declarations, and misses on a pooled task have no name at all.
*Without it:* the manager can see today's list, but not what keeps failing, where it keeps failing, or whether things are getting better.

**F22 — The exception view: what needs attention, and the manager sees her own.**
"Sunshine PG: 6 rooms not cleaned in 3 days", newest first, tapping through to the thing. The founder sees it across every property; **the manager sees the same list about her own property (D71)**. Never a side-by-side ranking of properties or managers (D46, D22).
*Without it:* the off-site owner is still asking managers how things are going, which is the position he pays us to get out of — and the manager is blindsided in a call by a list she has never seen, which is the fear that makes her kill adoption for everyone under her.

**F53 — A manager can complete a task on behalf of someone without a smartphone.**
Recorded as done by her, for them; those people are left out of automatic escalation (D30).
*Without it:* the guard's work is either missing from the system or permanently late — so F21's on-time rate and F22's exception list both show a failure that never happened, about the one person with no way to argue back.

**F15a — A "couldn't do it" outcome with a reason.**
Free text (D79).
*Without it:* blocked work looks identical to ignored work — "tenant was asleep" is recorded as a failure — and a person whose camera will not open has no way to submit at all.

**F24c — Skip or reschedule a single occurrence.**
Moved up from Band C (D67).
*Without it:* a festival or a one-off clash means turning the whole routine off, and often forgetting to turn it back on — and every festival day is recorded as the whole team failing, permanently.

**F51 — When someone leaves, their open work returns to the manager to reassign or close.**
Submitted proof keeps their name forever; their own tasks are archived (D25). Moved up from Band C — D25 calls staff departure *"the most common event in the system"*, and it fails D67's test: open tasks left on a person who has gone keep going overdue against them, so F21 and F22 report failures against someone who no longer works there.
*Without it:* in a business defined by churn, every resignation silently orphans that person's open work.

**F33a — Reassign a task, including by the person holding it.**
This is what replaces shift handover (D34, D25).
*Without it:* the guard going off duty at 10pm has to wake the manager to pass on his open work — or it stays his and goes overdue against him.

**F54 — When raising a complaint from a failure, show the open ones for that room first.**
The person adds this failure to an existing complaint — with today's photo — or starts a new one. They decide; the system does not match them automatically (D29, simplified per D65). *Build note: the move-out path looks up complaints with no open/closed filter, so the "show me the open ones" check is new work, not a pattern to copy.*
*Without it:* one leaking tap becomes seven complaints in a week, and the queue she relies on for real tenant issues becomes unusable.

**F8 — Link a task to a real thing, and see everything ever done to it.**
A due, tenant, room or asset — for context, filtering, navigation and history (D2). **History shows the submitted answers and photos, not only that a task happened** — that is what lets a supervisor audit a caretaker's work without a second system (D81).
*Without it:* "what has been done to room 204 this year" cannot be answered, which is the question a dispute or a handover actually asks — so the proof exists and nobody can find it.

---

## Band C — Completeness

**F14 — A comment thread on a task, with person tagging.**
*Without it:* when work fails there is nowhere to say why, so the manager phones to ask and the reason never reaches the record.

**F15b — Voice notes.**
*Without it:* staff who cannot type comfortably cannot explain anything, so they explain nothing.

**F24a — Quick-capture an ad-hoc task on the spot.**
*Without it:* the manager spots a problem on her walk-round and writes it on her hand.

**F24b — Approve and reject from the phone.**
*Without it:* review only happens when she is at a desk, so work waits.

**F33b — Assign one task across many rooms or people at once.**
*Without it:* setting up a 200-room property means doing it 200 times.

**F52 — When a manager is deactivated, her rules and pending reviews transfer.**
Rules do not fire under a dead account (D42).
*Without it:* a departed manager's routines keep generating work nobody owns, and her pending approvals block forever.

**F47 — New properties start with their routines already set up; existing ones are offered them.**
(D48.) The routines are **created, enabled and unassigned**, so nothing fires until the admin assigns someone — which needs the scheduler to skip routines with nobody on them (**P0**). He opens the app and sees the property already set up, with no failure history piling up against nobody (D78).
*Without it:* she has to do the setup work before she gets anything back. That is how a busy manager stops using something in the second week.

**F25a — A weekly digest to the owner over WhatsApp.**
Moved down from Band B (D78) — F22 is the promise; this is convenience on a screen that already exists.
*Without it:* the owner has to open the app to learn anything, and mostly he won't.

**F59 — A visit log: a task that records arriving and leaving.**
One field pair on top of an ordinary task — arrival with a photo, departure with a photo — so the record shows who was at the property, when, and for how long (D81). Everything else about it is a normal task: purpose, checklist, assignment, proof.
*Without it:* an operator whose staff travel between buildings cannot tell a real visit from a claimed one. S2L run ~50 buildings with supervisors doing about three a day, and asked for this directly; today they have no way to check a claimed visit happened.

**F1 — A task shows the live state of the thing it is linked to.**
"Room 204 · ₹8,000 due · PAID, 2 Aug", or "Partially paid, ₹3,000 of ₹8,000". It never judges whether the work is done — the person reads the real state and closes it (D65).
*Without it:* she checks another screen to find out whether the tenant paid before she can close the task — and the task list slowly disagrees with the alert it came from.

**F43 — A freeform tag on a task, for filtering.**
For anything with no entity behind it — "monsoon prep", "owner visit" (D21).
*Without it:* work that doesn't map to a room or tenant can't be grouped or found later.

**F25b — Export the task record for an audit or a dispute.**
Includes personal tasks (D26).
*Without it:* proving a year of compliance means screenshots.

**F27 — Task and rule creation built as a callable action, not a form-only path.**
(D10)
*Without it:* the assistant can never create tasks later without the work being redone.

---

## Later — deferred with a named home

- **F3b — The business creates tasks from its *other* events** (beyond move-out). **[V1.1]** — needs cross-module hooks. The move-out → room-prep half is **pulled into this cycle as F3** (D64 as amended), because the move-out lock path already exists and already raises complaints.
- **F6 — A recurring task that watches a condition** (standing rules). **[Later]** — deferred entirely (D64). Three of the five routines it was meant to serve turned out to be events, not conditions; one is already handled by complaint escalation; and we have no evidence yet for which conditions operators actually want. We will learn that by watching which tasks they keep re-scoping by hand. **Rule management, the reach preview and the self-pausing guard were originally dropped with it and are kept (F48/F49/F50)** — under D35 a rule *is* a repeating task, so those belong to F5 regardless.
- **F42 — Operator-managed areas** (lobby, lift, stairs) as pickable places with their own history. **[V1.1]** — tags carry it in V1.
- **F28 — Build tasks and rules by talking to the assistant.** **[Later]** — rides on the RentOk AI work; F27 keeps the door open.
- **F34 — Score a checklist from its ratings.** **[V2]**
- **F35 — Conditional items, and scanning an asset's code.** **[V2]**
- **Push notifications, email, a dedicated inbox.** **[V2]** — WhatsApp-first by decision, not omission (D17).
- **The full 65-entry system task registry.** **[Later]** — backend issue #6249.
- **Server-side drafts** beyond the on-phone save (D12). **[V1.1]**
- **Meter reading as a route** — many stops, each with a photo, monthly, gating invoicing. Needs a task target that isn't a room or property. **[V2]**
- **Advanced review** (approval chains, SLAs, auto-approve, bulk approve) · **advanced assignment** (delegation, out-of-office, load balancing, vendors) · **advanced escalation** (quiet hours, auto-close) · **request and work-order task kinds** · **shared-device kiosk mode** · **one task linked to many things at once**. **[V2]**

---

## Migrations and rollout

**M1 — Room cleaning becomes an ordinary recurring task, pooled per room.**
Existing schedules carry over untouched; the shortcut button stays and creates a normal task underneath (D60).
*Why it matters:* today no room has a recorded owner — everyone gets one shared link. This is what gives the most common task in the building a doer.

**M2 — The access-control migration: managers keep today's access, staff default to seeing only their own.** (D69, correcting D13.) The line is `view_team`/`add_team`/`edit_team` — anyone without them starts at "see only my own." **Runs with M1**, because room cleaning today gives every staff member one shared link, so tightening before M1 would leave a cleaner with nothing.

**M3 — System-raised tasks are mapped onto the shared categories.** (D45, F57)

**M4 — Released to everyone, enabled account by account.**
Ships to all users — there is no pilot (D47) — but enablement is controlled per account so it can be rolled forward over days and stopped instantly (D49).
*Why it matters:* notification volume at scale and the access migration are the two things that bite hardest and cannot be undone once wide.

---

## Not building


- **High-frequency logs** — anything recorded several times a day, such as a motor's on/off times. That is telemetry, not work; a due date is the wrong shape for it (D81).
- **Tracking licence and certificate expiry dates** — a fire NOC or trade licence renewal is a recurring task with a date the manager sets. We do not track expiries or warn before them (D81).
- Fines, salary deductions, or a staff scorecard — for anyone, including managers (D15, D22).
- Anything that acts without a person confirming (D1).
- A free-form if-this-then-that rule builder (D7).
- Attendance and shift clocking · a native Task tab (D11) · rebuilding move-in/move-out (D14, D61) · the guard's visitor register.

## What changed in version 2.1

Stress-tested after v2.0 and cut. **Standing rules are deferred entirely** (D64) — F6, rule management, preview and guards all go, along with the stored room-occupancy prerequisite. **A task no longer suggests that work is done; it shows the live state of the thing it is linked to** (D65) — that removes a hand-written rule for every kind of linked thing, each of which could be wrong. **An alert can now be turned into work, one task per item** (D66, F58) — the small bridge that lets a detected problem become work with an owner. Operator-managed areas moved to V1.1; complaint matching became "show the open ones and let the person choose"; checklist editing is blocked while tasks are open rather than version-pinned.

*(The v2.1 line "vacant-room readiness has no home in V1" is superseded by v2.2 — F3 is pulled in.)*

## What changed in version 2.3

The story widened to the property's whole work, not its routines (D81) — which added **F59** (a visit log: one task that records arriving and leaving), tightened **F8** to say history shows the submitted answers and not only that a task happened, and put high-frequency logs and licence-expiry tracking explicitly in *Not building*. The problem is now stated as late discovery rather than lazy staff (D82), and the cost chain behind it is measured rather than asserted (D83). **F51 moved C → B** — a resignation currently leaves open work going overdue against someone who has left, which makes F21 and F22 report failures about a person who no longer works there.

## What changed in version 2.2

An adversarial round-2 review, worked finding by finding (D67–D79; log at [review-round-2-decisions.md](review-round-2-decisions.md)).

**The bands gained a rule** (D67): if a Band B feature would produce a false record without it, or would break another part of RentOk, it is Band B. That moved **five items up from C** — F53, F15a, F24c, F33a, F54 — each of which was letting a Band B feature lie. **F8 also moved up** (D68), which is what D45 had already decided, and the Brief stopped calling the entity link ship-blocking. **F47 and F25a moved down to C** (D78).

**Two v2.1 calls were amended.** F48/F49/F50 are **kept**, reframed as managing ordinary repeating tasks — under D35 a rule *is* a repeating task, so F5 needs them regardless. And **F3 is pulled into this cycle**, so vacant-room readiness does have a home: a finished move-out creates the prep task, using a seam that already exists.

**Two locked decisions were corrected.** D13 would have shipped every permission open to everybody, making D55 false in production on day one — staff now default to "see only my own" (D69). D22 promised the manager a first look that nothing implemented — she now sees the same exception list about her property that the owner sees (D71).

**Also:**
- Per-person numbers come from fan-out only, never from pooled self-taps (D70)
- Notifications gained a reminder before the deadline, and a send time set per property (D73)
- Photos are deleted from the phone after they upload (D75)
- Translation is deferred and the manager writes in her own script, but starter templates ship bilingual (D76)
- Duplicate tasks are explicitly allowed (D77)
- New properties get their routines created unassigned, and the scheduler must skip routines with nobody on them (D78, P0)

**Three review findings were wrong and were corrected by Sanchay or by a code check:** the monthly building audit is one form, not 200 tasks (D72) · staff do not share phones, which invalidates a persona claim carried in three docs (D74) · and "unassigned means nothing runs" was not true of the scheduler, so D78 needs a real code change rather than none.

## What changed in version 2.0

Reconciled against all 63 decisions. **F23 (shift handover) is removed** — it is reassignment (D34). Compound requirements were split so each can be cut independently (F15a/b, F24a/b/c, F25a/b, F33a/b). **Eighteen requirements were added** that the decisions created but the list never captured — including both prerequisites, runner identity, camera-only, template versioning, areas, starter routines, rule management, and the people-lifecycle items. Every requirement now carries what the operator loses without it, and the tags were replaced with a cut order ranked by user-miss rather than by build cost.
