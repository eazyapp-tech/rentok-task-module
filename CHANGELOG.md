---
title: "Task Module Redesign — CHANGELOG (source of truth)"
date: 2026-07-21
owner: "Sanchay"
status: "current"
tags: [rentok, tasks, changelog]
---

# Task Module Redesign — CHANGELOG

This file is the source of truth for the Task module redesign. Every other doc — the vision brief, the PRD, the workflow specs, the pre-mortem — defers to it. **If a doc says something this file contradicts, this file is right and the doc is stale.**

Two things live here: the **canonical sentences** (the exact words to use everywhere) and the **numbered decisions** (D1…Dn — stable IDs the other docs cite instead of restating).

---

## The canonical sentences

These are the words. Use them exactly, everywhere. They are the model, settled.

> **1. Nothing acts on its own — the system suggests, the person confirms.** No task closes itself, no complaint is raised without someone raising it, no standing rule switches on without an operator turning it on.

> **2. A task tied to a real thing reads that thing's state and suggests; it does not write into it.** A task on a rent due suggests closing when the due is paid — it never marks the due paid.

> **3. Entity linking is for context, filtering, navigation, and history — not for driving completion.** The one status-driven suggestion is a task tied to a due, whose whole purpose is a paid/unpaid state.

> **4. A task and a complaint linked to it run on separate statuses.** Closing one does not close the other; you can move between them from either side.

> **5. Three sources of task, one place.** The system raises it, a person assigns it, or a person keeps it for themselves — and the manager sees all three in one category-filtered list.

> **6. The module's jobs, in order: tell people what to do, prove it was done, let each level see and help.** Tell → prove → see. Telling is the everyday heart, and it is help, not oversight.

> **7. The proof belongs to the person who collected it — their defense first, the record second.** No fines, no scorecard, this cycle or next.

> **8. A property runs on a system, not on one person's memory** — so it survives the manager's absence and the staff churning.

> **9. A standing rule is the campaign model operators already know: a curated trigger, a cadence, and a stop condition — recommended and shipped ready to switch on, not a free-form rule builder.**

> **10. A task assigned to several people works one of two ways: done by any one (it closes for all — "pooled"), or done by each one separately (each has their own copy — "fan-out"). The creator picks. Either way, the system records who did what.**

> **11. Open-ended power comes through the assistant later — build tasks and rules by talking, grounded in the real property.** This cycle only makes creation a callable action so the assistant can plug in.

> **12. Move-out is an existing task that already records deposit deductions — reuse it, do not rebuild it.**

---

## Numbered decisions

Each decision is a stable ID (D1…). Cite the ID, do not restate the decision. Each records the call, why, and the alternative rejected — so we do not re-litigate.

### D1 — Suggest, never auto
The system suggests a completion or an action; a person confirms. **Rejected:** silent auto-complete when a linked entity resolves — a person may have done the wrong KYC/Aadhaar, and removing human judgment on a legal/financial record is unsafe.

### D2 — Entity linking is context, not a write-back engine
A task references an entity (due, tenant, room, asset) for context, filter, navigation, and history. **Rejected:** the earlier "five outward write-backs" model — it miscast the link as a channel for the task to write into rent/KYC/assets. The task reads and suggests; it does not write.

### D3 — Due-linked completion is a read-time check, not an event push
When the task list is opened, the module reads the linked due's status and suggests closing if paid. **Rejected:** an event bus that pushes "due paid → touch the task" — no event bus exists in the codebase (every automation is a cron that polls and evaluates), and read-time resolution is cheaper and fits the grain.

### D4 — Task ↔ complaint: linked, independent status
A task can raise a complaint (details pre-filled) and each can navigate to the other; their statuses are independent. **Rejected:** linking the statuses — the inspection task is done when the inspection happens; the complaint stays open until the fix, so linking them would hold one hostage to the other.

### D5 — Three sources, one manager list
System-raised, manager-assigned, and self-kept tasks all appear in the manager's one category-filtered list. **Rejected:** walled separate systems for each source.

### D6 — Type-1 system pending tasks stay as-is this cycle
The existing system-detected pending tasks (the home feed) are left as they are; Type-2 tasks are added into the same list. **Rejected:** rebuilding the full 65-entry registry now — deferred to a later phase (GitHub issue eazyapp-tech/rentok-backend#6249).

### D7 — Standing rules are curated + recommended, not a free-form builder
Operators pick from a curated set of meaningful triggers RentOk recommends and ships ready to switch on, with a cadence and a stop condition. **Rejected:** an if-this-then-that condition editor — that is a developer tool, not an operator tool. Open-ended power comes through the assistant (D10), not a builder UI.

### D8 — Two multi-assignee modes: pooled and fan-out
A task assigned to several people is either **pooled** (any one completes it and it closes for all — "someone clean the lobby") or **fan-out** (each person gets their own copy and must complete it separately — "each guard does their own round"). The creator picks the mode; either way the system records who did each one. **Rejected:** supporting only one mode — real operations need both, and dropping who-did-it attribution would break the proof (D15).

### D9 — Self-to-dos are a first-class source
Anyone can create a task or log for themselves, with a reminder, and set recurring routines for themselves or the people they lead. **Rejected:** manager-assigned-only — people at every level need their own routine, and this is the everyday enablement value.

### D10 — AI creation is a future door; this cycle builds creation as a callable action
Building tasks and standing rules by talking to RentOk's assistant is the horizon (power + accessibility for Hindi-first users), grounded in the real property. **This cycle** only requires that task and rule creation be a callable action, not a form-only path, so the assistant can plug in later. **Rejected:** form-only creation that the assistant could never reach.

### D11 — Runner via web-view + registry deeplink, not a native tab
The staff runner and the manager's list reach the mobile app through the existing web view. **Rejected:** a native bottom-nav Task tab — a large mobile build for no user-visible gain this cycle.

### D12 — Partial save is client-side this cycle
Partial work is held on the phone and submitted on reconnect. **Rejected (as this-cycle):** a server-side draft — a fast-follow, not the launch requirement.

### D13 — Access-control migration defaults every user to today's access
The new access control ships defaulting every existing user to the access they have now; it tightens deliberately later, per property. **Rejected:** default-off — it would lock managers and staff out of their tasks on release day.

### D14 — Reuse move-out, do not rebuild it
The move-out checklist is an existing task that already records deposit deductions and turns them into invoices and expenses. The redesign connects to it. **Rejected:** rebuilding deposit-deduction logic inside the new module.

### D15 — No fines, no scorecard — the bet
No built-in fines, salary deductions, or staff scorecard, this cycle or next. **Rejected:** any deduction feature — staff who believe the tool can cost them pay stop filling it honestly, and the honest proof the whole product runs on collapses.

### D17 — Notifications are WhatsApp-first; push and email are deferred
This cycle, all task notifications (reminders, escalation, @mentions, the owner digest) go over WhatsApp — the channel this audience actually reads. The staff member's own list (F17) and the manager's list (F20) serve as the in-app inbox. **Push and email are [v2]**, not omissions. **Rejected (as this-cycle):** building push/email/inbox now — WhatsApp reaches everyone here and the others add channels to maintain for little gain at launch. Recorded because both are competitor table-stakes and their absence must be a decision, not a hole.

### D18 — A failed check routes to a complaint, not a separate "corrective task"
When a check fails, the corrective action is the pre-filled linked complaint (F2), which enters the real complaint queue and its escalation. **Rejected:** auto-creating a distinct "corrective task" type on fail — it duplicates what the complaint already does and splits the same problem across two objects. The complaint *is* the corrective action.

### D16 — Configurable schedules; the engine already supports it
Recurring cadence (daily/weekly/monthly) is operator-editable. The task engine already supports these; today's "daily only" room cleaning is a UI default, not a structural limit — so this is surfacing a field, not new engine work (custom cadences like every-N-days would need more). **Rejected:** leaving schedules frozen as they are today.

---

---

# Decisions from the 2026-07-21 grilling session (D19–D45)

Locked with Sanchay, question by question, after the three-lens product review and four codebase grounding sweeps.

## The shape of the work

### D39 — This cycle is "finish the task engine", not "build a rules engine"
The codebase has built the two halves of a standing rule three times and never joined them: the **task scheduler** produces real work but is clock-only with two scopes and four cadences; the **home-screen pending feed** watches ~11 conditions and reappears nightly but produces only a count with no owner; the **review campaign scheduler** has conditions *and* richer cadences (daily/weekly/biweekly/monthly/48h, day-of-week, day-of-month, multi-property) but its output is a WhatsApp message to tenants. A standing rule is the missing join. So the cycle is framed as completing the engine — real scope, an optional condition, a stop condition, richer cadences, an edit path, per-instance assignment, missed-cycle integrity — with the condition as one field among several fixes. **Rejected:** shipping "standing rules" as a separate named feature — it re-creates two ways to do the same thing, right after D35 said a rule *is* a recurring task.

### D40 — The trigger has no scheduler in the repo; verifying it is a prerequisite
`POST /tasks/trigger` is unauthenticated and there is no cron registration anywhere in the codebase — every recurring task depends on an unidentified external caller. Confirm what calls it today; if nothing reliable does, a proper scheduled job plus authentication becomes prerequisite work for the whole cycle. **Why it outranks everything:** every recurring feature specced here sits on top of it.

### D35 — A standing rule is a recurring task with a condition
Creating a rule *is* creating a recurring task: pick a checklist, a cadence, people, and for "where" pick a condition instead of a fixed list. **Rejected:** a separate Rules section — one extra choice in a known flow beats a second way to create work. The schedule table's existing scope field carries the condition.

### D37 — Rules start when the schedule says, and show their reach first
A rule carries a start date and time like any recurring task, defaulting to now and editable — so "start now" and "start tomorrow" need no special logic. Before switching on, the operator sees how many rooms or people currently match. **Rejected:** a bespoke first-run rule; the recurring-task model already answers it.

### D36 — Persist whether a room is empty
There is no stored occupancy signal; it is derived by joining rooms → beds → tenants on every check. The headline routine (prepare empty rooms until they fill) would run on the slowest check in the system. Store and maintain the flag. **Rejected:** recomputing each sweep (too slow at scale) and dropping room rules from v1 (that is the routine with real money in it — a 7–10 day readiness window).

## Assignment, identity and people

### D19 — Pooled tasks capture who did it; fan-out already identifies by token
Fan-out already creates one instance per person, each with its own token and member id, so attribution exists by construction. A **pooled** task records no one — so the runner asks "who did this?" from the assigned list before submit. **Rejected:** full actor-select on every runner open (friction on every task for the cheap-phone user, against the 3-second gate) and doing nothing (a pooled task's proof would protect nobody).

### D27 — A rule's assignees are chosen when it is set up
Exactly like a recurring task today. **Rejected:** role-based routing for v1 — it needs accurate roles, and it can wait.

### D25 — When someone leaves, their open work goes back to the manager
Open tasks return to the manager, who can reassign **or** close them. Submitted proof keeps its author permanently. Their own to-dos are archived, not deleted. **Why:** high churn is the norm here; this is the most common event in the system.

### D30 — People without a smartphone can have work recorded for them
A manager can complete a task on someone's behalf, and it records that she did it for them. Those people are excluded from automatic escalation. **Rejected:** blocking assignment (their work vanishes from the record) and assigning-without-escalation (a list nobody reads).

### D34 — Handover is reassignment, not a shift feature
There is no roster data and attendance is out of scope, so "shift handover" is just reassigning open tasks — and **the assignee can reassign their own**, so the guard going off at 10pm doesn't need the manager. Multi-select so several move at once. **Rejected:** building shifts/rosters; F23 as a separate feature is dropped.

## Time, cadence and repetition

### D23 — Every period is its own obligation
Monday's cleaning and Tuesday's cleaning are two different jobs. An unfinished one closes as **not done** when the next one fires — it is never silently rolled forward, because that hides the miss the record exists to capture. Four consequences: a **late submission is accepted and marked done-late** (never rejected — the work is real and the signal is bad); **partial proof is preserved** on a missed task; **repeats collapse in the list** so 22 misses read as a count, not 22 rows; a cadence shorter than the job is a setup warning, not special logic. **Note:** the scheduler today silently discards backlog and resets to tomorrow — this decision is a fix to that behaviour, not only a spec.

### D33 — Rules that produce only ignored work pause themselves
Two guards: **fired 5 times with zero completions ever** → pause (catches a bad setup within days), and **no completion from the rule in 14 days** → pause (catches abandonment while surviving a festival week). Pausing stops new tasks only; nothing is deleted, and the manager is told why.

## Notifications

### D24 — One daily summary, plus immediate messages for things that can't wait
Scheduled work arrives as **one message per person per day** carrying all their task links — this protects the only channel we have from throttling and from being muted. **Newly assigned ad-hoc work and rejected/sent-back work notify immediately**, since those are events the person needs now and are low-volume. Escalation sends on its own, rate-capped, inside a sane daytime window so nobody is woken at 2am.

## Locations and linking

### D20 — Scope is picked like a complaint's location, but as a set
Property → floor → room, mirroring the complaint location picker the operator already knows, but multi-select with an "all rooms" option, because a task covers a set where a complaint covers one place. The engine already resolves all-rooms, by-floor and a chosen list; only the create endpoint needs to accept them.

### D21 — Rooms and operator-managed areas are real; tags are a filter escape hatch
Places that aren't rooms (lobby, lift, stairs, terrace) are added once per property as **areas** and picked like rooms — real things that carry history and can be targeted. Separately, a **freeform tag field** covers anything with no entity at all ("monsoon prep", "owner visit"), **for filtering only**. Two guardrails: tags are labelled *Tags* and kept away from *Location* (or the area list rots as people type instead), and previously used tags are suggested as they type (kills most drift). **Rejected:** freeform-as-location (drift breaks filters and reporting) and faking areas as rooms (rooms feed occupancy and availability — a fake "Lobby" room would skew reports).

### D28 — Several tasks may target the same place at once
A room mid-move-out can also match a cleaning rule, and both tasks exist; people sort out the order. **Rejected:** excluding rooms in transition — over-engineering, and sometimes both jobs genuinely are needed.

## Failures and complaints

### D38 — A failed check raises its complaint with one tap, on by default
S2L's top ask is automatic ticket creation from a failed audit item. The person is already there filling the form, so a single confirming tap is not friction — and it stops a mistyped answer becoming a live ticket with real escalation. It is effectively automatic from their side while keeping D1 intact. **It reuses the complaint module's existing category → responder routing, so "assign it to Pankaj the electrician" comes free.**

### D29 — A repeat failure joins the open complaint instead of raising a new one
If a complaint is already open for the same checklist item on the same place, today's failure is added to that complaint's existing remark thread — **carrying that day's photo** — so a week of failures reads as one thread with seven dated photos. The matching key is *same checklist item + same place + complaint still open*. A **resolved** complaint means the next failure starts a fresh one — real signal that the fix didn't hold. No extra escalation logic: the complaint clock already runs on time since raised.

## Trust and visibility

### D22 — The no-scorecard promise covers the manager too
No ranking of managers or properties against each other — the owner sees what needs attention, not a league table. She gets her own record as her defense, exactly like staff. Escalation reaches the owner only after she has had a fair chance to see it first. **Why:** the same logic as the staff bet — a manager who feels watched hides problems, and then the owner's view is full of lies anyway. She is a named top-3 adoption risk.

### D26 — Personal to-dos are in the owner's export, and are never called private
The feature is named **"My tasks"**. It appears in the owner's export and audit log. The word *private* is never used in the UI, help text or training, so no promise is made and none is broken. **Rejected:** calling them private while exporting them — one manager finding their "private" note in an owner's export would kill the trust the whole module is betting on, permanently, for a small reporting gain.

### D31 — One list, sorted by due date then priority
Across all three sources. **Rejected:** keeping the money-tuned urgency tiers on top (a cleaning task due in an hour would sit below a low-priority money alert) and grouping by kind (she'd scan three blocks to find what's urgent).

### D32 — Text a manager types herself is shown as typed
Ready-made checklists are translated; her own free text is not. She knows her staff and already writes to them in their language on WhatsApp every day, and auto-translating an instruction risks changing its meaning.

## Behaviour decisions (D41–D45, decided rather than debated)

### D41 — Rule lifecycle
Editing or deleting a rule affects **future** tasks only; tasks already created finish normally and keep their proof. Narrowing scope cancels only not-yet-started instances. When an entity stops matching, the open task **suggests closing** — it never closes itself (D1). Rules can be listed, edited, paused, resumed and archived, and show what they have produced.

### D42 — Ownership and dangling references
When a manager is deactivated, her rules and pending reviews transfer to the owner or a named successor, and rules do not fire under a dead account. If a linked thing is deleted or merged, the suggestion goes quiet and the task survives and stays completable.

### D43 — Money states are read strictly
Only a **fully settled** due suggests closing its task. Partial, refunded, reversed or waived never do — a task suggesting "done" because a due was waived is a wrong suggestion on a money record.

### D44 — Data integrity
Each task keeps the checklist version it was created from, so editing a live template never invalidates answers already given. Overdue is always computed by the server on sync, never the phone's clock, and missed reminders collapse into one message. One-off tasks can be created offline and sync later; recurring schedules and rules need a connection.

### D45 — List consistency and races
System-raised tasks are mapped onto the same categories so one filter works across all three sources. The "every task ever on this room or tenant" history view gets built — it is what linking is for. If two people finish a pooled task at once, the first to sync wins and the second is kept as supporting proof. Reassigning mid-task never carries the first person's unsubmitted proof under the new person's name.

## Rollout, conditions and quality bars (D46–D53)

### D47 — This is a feature extension for every user, not a pilot
It ships to all accounts. There is no pilot property, so the ship gates must hold for everyone on day one, and the access-control migration (D13) reaches every account at once. **This supersedes any "first real property" framing in the pre-mortem and the launch measurement.**

### D49 — Released to everyone, enabled account by account
The feature is built and released for all users, but enablement is controlled per account so it can be rolled forward over a few days and stopped instantly. **Why:** notification volume at scale and the access migration are the two things that bite hardest and are hardest to undo once wide. This is a safety valve on a general release, not a pilot.

### D48 — New properties start with routines running; existing ones get suggestions
A newly created property begins with the standard routines for its type already on. A property that already has its own setup sees them as one-tap suggestions and nothing changes until accepted. **Rejected:** switching routines on everywhere — pushing running tasks into a property that already works its own way creates duplicate work and reads as a broken release to the users we already have.

### D50 — Four condition groups ship in v1
Grounded in the registry entries that are both physical work and recurring-while-true:

| Group | Condition → routine | Registry entries |
|---|---|---|
| **Room** | is empty → prepare until filled · about to empty → pre-inspection | — (the 7–10 day readiness window) |
| **Money** | dues overdue → collection visit until paid | A1, A4 |
| **Documents** | missing KYC / unsigned agreement / police verification pending → collection visit until on file | C2, C3, C5, C7 |
| **Complaints** | open past its time → re-visit until closed | D2, D8, D9 |

**Correcting an earlier call:** document collection was initially dismissed as desk work that the existing alert already covers. That was wrong — the registry is explicit that someone physically visits the tenant to collect and scan the document, and repeats until it is on file. The tenant-side conditions reuse the filter lookup already running in production for announcements, so most of this is wiring rather than new logic.

*Noted, not scoped:* meter readings (D7) are a **route** — many physical stops, each with a photo, monthly, gating invoicing — and would need a task target that isn't a room or a property. Room inspections (D3/D7) are expressible as plain recurring tasks once scope lands (D20); inspections were never actually built.

### D46 — The founder gets an exception list and a weekly digest
A screen listing what needs him now — "Sunshine PG: 6 rooms not cleaned in 3 days" — newest first, each line tapping through to the thing. Plus the weekly WhatsApp digest for rhythm. **Never a side-by-side ranking of properties or managers** (D22).

### D51 — Test the bet by comparing properties, never people
Track completion rate and proof quality per property, and interview staff at a handful of properties in week 4. No individual-level comparison is built or reported. **Why:** it answers "are staff filling this honestly" without building the people-ranking D22 forbids.

### D52 — Photos are camera-only, always; location is per checklist
A photo question opens the camera and never the gallery — no exception, no setting. Gallery upload is the single hole that makes every photo worthless as evidence, and S2L already treats camera-only as settled. Whether location must be verified against the property is set per checklist, since a room inspection needs it and a desk task does not.

### D53 — Review is set per checklist and is off by default
A daily cleaning task is done when the person submits it. A move-out inspection or an audit switches review on. **Rejected:** reviewing everything — 200 approvals a day recreates the exact workload we are removing, and she would bulk-approve without looking, which is worse than no review. **Also rejected:** reviewing only failures — a faked pass is the submission you would most want a human to see.

---

## What this supersedes

- The **"five outward write-backs"** framing in the v0 brief (preserved at `archive/task-module-brief-v0-original-2026-07-18.md`), and in the superseded PRD and pre-mortem (`archive/task-module-prd-SUPERSEDED.md`, `archive/task-module-pre-mortem-SUPERSEDED.md`). Tasks do not write back into dues/KYC/assets — see D2. Any doc that says a task "marks the invoice paid" or "marks the tenant verified" is superseded by this file.
- Any framing of the checklist template library as a **parallel workstream** — it is one feature requirement inside this redesign.

## Changelog of this changelog

- **2026-07-21** — Created as the source of truth. Locked 12 canonical sentences and 18 decisions (D1–D18) from the design conversation. Supersedes the outward-write-back model. (Product-lens review pending: a batch of behavior decisions D19+ to be added, and D16 to be reordered after D15.)
