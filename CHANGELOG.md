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

> **1. Nothing acts on its own — a person decides.** No task closes itself, no complaint is raised without someone raising it, and no alert becomes work without someone turning it into work.

> **2. A task tied to a real thing shows that thing's live state; it does not write into it, and it does not judge whether the work is done.** A task on a rent due shows *"₹8,000 · PAID, 2 Aug"* — it never marks the due paid, and it never decides the task is finished.

> **3. Entity linking is for context, filtering, navigation, and history — not for driving completion.** Nothing about the linked thing closes a task. The person reads the state and decides.

> **4. A task and a complaint linked to it run on separate statuses.** Closing one does not close the other; you can move between them from either side.

> **5. Three sources of task, one place.** The system raises it, a person assigns it, or a person keeps it for themselves — and the manager sees all three in one category-filtered list.

> **6. The module's jobs, in order: tell people what to do, prove it was done, let each level see and help.** Tell → prove → see. Telling is the everyday heart, and it is help, not oversight.

> **7. The proof belongs to the person who collected it — their defense first, the record second.** No fines, no scorecard, this cycle or next.

> **8. A property runs on a system, not on one person's memory** — so it survives the manager's absence and the staff churning.

> **9. An alert can be turned into work — one task per item, with an owner and a record.** RentOk already notices what is wrong; turning that into work someone owns is the new part, and a person decides who.

> **10. A task assigned to several people works one of two ways: done by any one (it closes for all — "pooled"), or done by each one separately (each has their own copy — "fan-out"). The creator picks. Either way, the system records who did what.**

> **11. Open-ended power comes through the assistant later — build tasks and rules by talking, grounded in the real property.** This cycle only makes creation a callable action so the assistant can plug in.

> **12. Move-out is an existing task that already records deposit deductions — reuse it, do not rebuild it.**

---

## Numbered decisions

**Finding a decision:** D1–D18 the design conversation · D19–D45 the 2026-07-21 grilling · D46–D63 rollout,
trust and content · D64–D66 reversals · D67–D79 the 2026-08-03 review · D80–D83 the moat and the problem.
Within a group the order is by topic, not by number.

Each decision is a stable ID (D1…). Cite the ID, do not restate the decision. Each records the call, why, and the alternative rejected — so we do not re-litigate.

### D1 — Nothing acts on its own; a person decides
*(Retitled 2026-08-04. The principle is unchanged; the word "suggests" is dead — see D65.)*
The system shows state and pre-fills; a person decides. **Rejected:** silent auto-complete when a linked entity resolves — a person may have done the wrong KYC/Aadhaar, and removing human judgment on a legal/financial record is unsafe.

### D2 — Entity linking is context, not a write-back engine
A task references an entity (due, tenant, room, asset) for context, filter, navigation, and history. **Rejected:** the earlier "five outward write-backs" model — it miscast the link as a channel for the task to write into rent/KYC/assets. The task reads and suggests; it does not write.

### D3 — Due-linked completion is a read-time check, not an event push
*(Superseded by D65 — the read-time part survives, the suggestion does not. A task **shows** the due's state; it never proposes that the work is done.)*
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
*(Corrected by D69 — "today's access" means everything, because the task controller checks nothing today. Staff now default to "see only my own.")*
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
*(Superseded by D64 — no condition ships at all. What survives is the framing: the fixes listed here — real scope, richer cadences, an edit path, per-instance assignment, missed-cycle integrity — are the cycle, minus the condition.)*
The codebase has built the two halves of a standing rule three times and never joined them: the **task scheduler** produces real work but is clock-only with two scopes and four cadences; the **home-screen pending feed** watches ~11 conditions and reappears nightly but produces only a count with no owner; the **review campaign scheduler** has conditions *and* richer cadences (daily/weekly/biweekly/monthly/48h, day-of-week, day-of-month, multi-property) but its output is a WhatsApp message to tenants. A standing rule is the missing join. So the cycle is framed as completing the engine — real scope, an optional condition, a stop condition, richer cadences, an edit path, per-instance assignment, missed-cycle integrity — with the condition as one field among several fixes. **Rejected:** shipping "standing rules" as a separate named feature — it re-creates two ways to do the same thing, right after D35 said a rule *is* a recurring task.

### D40 — The trigger has no scheduler in the repo; verifying it is a prerequisite
`POST /tasks/trigger` is unauthenticated and there is no cron registration anywhere in the codebase — every recurring task depends on an unidentified external caller. Confirm what calls it today; if nothing reliable does, a proper scheduled job plus authentication becomes prerequisite work for the whole cycle. **Why it outranks everything:** every recurring feature specced here sits on top of it.

### D35 — A standing rule is a recurring task with a condition
*(Superseded by D64 — standing rules are deferred. The half that survives is the framing itself: a rule **is** a repeating task, which is why F48/F49/F50 are kept for repeating tasks even though F6 is gone.)*
Creating a rule *is* creating a recurring task: pick a checklist, a cadence, people, and for "where" pick a condition instead of a fixed list. **Rejected:** a separate Rules section — one extra choice in a known flow beats a second way to create work. The schedule table's existing scope field carries the condition.

### D37 — Rules start when the schedule says, and show their reach first
*(Superseded by D64 as to rules. The reach preview survives as **F49**, applied to repeating tasks — where it matters more: "this will create 200 tasks every day.")*
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
*(Superseded by D64 as to rules. The guard itself survives as **F50**, applied to repeating tasks.)*
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
*(Reduced by D71 — the last sentence originally promised her a first look. Nothing implemented it, so it now reads as built: she sees the same exceptions the owner sees, at the same time.)*
No ranking of managers or properties against each other — the owner sees what needs attention, not a league table. She gets her own record as her defense, exactly like staff. She sees the same exceptions about her property that the owner sees, at the same time — so she is never blindsided (D71). **Why:** the same logic as the staff bet — a manager who feels watched hides problems, and then the owner's view is full of lies anyway. She is a named top-3 adoption risk.

### D26 — Personal to-dos are in the owner's export, and are never called private
The feature is named **"My tasks"**. It appears in the owner's export and audit log. The word *private* is never used in the UI, help text or training, so no promise is made and none is broken. *(Accepted risk, recorded 2026-08-04: because she is told her own notes reach the owner's export, the manager most likely to need "My tasks" is the one most likely to keep using paper. Low F7 adoption is expected, not a bug. The alternative — carving self-tasks out of the export — was considered and not taken.)*

**Rejected:** calling them private while exporting them — one manager finding their "private" note in an owner's export would kill the trust the whole module is betting on, permanently, for a small reporting gain.

### D31 — One list, sorted by due date then priority
Across all three sources. **Rejected:** keeping the money-tuned urgency tiers on top (a cleaning task due in an hour would sit below a low-priority money alert) and grouping by kind (she'd scan three blocks to find what's urgent).

### D32 — Text a manager types herself is shown as typed
Ready-made checklists are translated; her own free text is not. She knows her staff and already writes to them in their language on WhatsApp every day, and auto-translating an instruction risks changing its meaning.

## Behaviour decisions (D41–D45, decided rather than debated)

### D41 — Rule lifecycle
Editing or deleting a rule affects **future** tasks only; tasks already created finish normally and keep their proof. Narrowing scope cancels only not-yet-started instances. Rules can be listed, edited, paused, resumed and archived, and show what they have produced.

*Partly dead (D64, D65): there are no conditions left to stop matching, and nothing suggests any more. The management half survives as **F48**, applied to repeating tasks.*

### D42 — Ownership and dangling references
When a manager is deactivated, her rules and pending reviews transfer to the owner or a named successor, and rules do not fire under a dead account. If a linked thing is deleted or merged, the task stops showing that thing's state (D65) and survives, still completable.

### D43 — Money states are read strictly
*(Mostly superseded by D65 — nothing suggests closing any more. What survives is the display rule: partial, refunded, reversed and waived are shown as what they are, never as settled.)*
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

## Trust, permissions, content and semantics (D54–D63)

### D54 — Harden the runner without adding a login
Today `submitTask` takes the **doer's identity and the location from the request body** and stores them unverified, on a route with no authentication and a token that never expires. Only the timestamp is trustworthy — so the proof is a self-report, and D52 and D19 are honour systems. Three fixes, none of which adds a login step (a login is a drop-off the 3-second gate cannot afford):
1. **Identity comes from the task record, not the caller.** Fan-out instances already store the person; stop accepting an identity parameter. Pooled tasks use the on-device name tap (D19).
2. **Links expire** when the task's window closes, instead of working forever.
3. **Location is checked server-side against the property and recorded — never blocking.** GPS fails indoors and on cheap phones; blocking would stop real work and staff would blame the app. A mismatch is flagged for the reviewer instead.

Honest limits, accepted: a person can still forward their own link, and coordinates can be faked by a determined caller. Closing those needs a login and device attestation, which cost more adoption than they are worth. This moves us from "anyone can submit as anyone" to "the record is honest unless deliberately gamed."

### D55 — Staff see only their own tasks and their own record
Not other people's tasks, not other people's completion. **Why:** the moment staff can see each other's numbers we have built the leaderboard the bet forbids — by the back door, without anyone deciding to. It also keeps the runner small, which the cold-load gate needs.

### D56 — Creating a rule rides on the permission to create tasks
**This corrects an earlier recommendation.** A separate manager-only permission for rules was proposed on blast-radius grounds; that argument fails, because anyone who can create a recurring all-rooms task already generates hundreds of tasks a day. **The blast radius comes from scope, not from the condition** — and D35 already established that a rule *is* a recurring task, so a separate permission would re-split what we merged. If a guard is ever wanted, it belongs on scope ("who may target all rooms").

### D57 — Five permission flags
See tasks at the property · see only my own · **create and assign** (includes editing, and covers rules) · review (approve/reject) · **archive**. Editing work you assigned is ordinary; making a record disappear — even recoverably — is the one action nobody notices until they go looking, so it gets its own gate. Everyone keeps the access they have today when this ships (D13).

### D58 — Recommendations come from property type and enabled modules
On day one we recommend from what we actually know: PG / co-living / hostel, rough bed count, and which modules are on (food, meters, move-in/out). A food-enabled 60-bed PG gets a different starter set than a 200-bed co-living. **Learning from what similar properties keep running is the stronger version and comes later** — it needs adoption first, and today only a handful of properties use the module. Do not market the learned version yet.

### D59 — RentOk's templates are the starting point; the operator can change them
She can copy any template and edit its questions, or build one from scratch. **Why:** the blank box is the problem F9 exists to solve, but every property has quirks, and a template she cannot adjust is abandoned on first contact with reality. The engine already supports operator-authored question lists.

### D60 — Room cleaning becomes an ordinary recurring task, pooled per room
*(Half-fixed, per D70: a **completed** pooled room records who did it; a **missed** one has no owner at all and belongs to the cleaning team, not a person.)*
Today it is a hardcoded path: one task per room, **assigned to nobody**, with every staff member getting one shared link — so no room has an owner. It becomes a normal recurring task with all-rooms scope, **pooled** across the cleaning team: any of them can do a room, and whoever does taps their name (D19), so each room finally has a recorded doer. Existing schedules carry over untouched and the shortcut button stays, creating a normal task underneath. **Rejected:** fan-out per person — 200 rooms × 3 cleaners is 600 tasks a day, and somebody would have to decide who cleans which room every morning.

### D61 — Move-in/move-out stays as it is; we connect at the edges
It is a separate, tenant-facing, one-shot system that handles deposits, damage costing and invoices, and money depends on it (D14). We do not converge it. We connect: a room going through move-out can trigger the prep routine, and a failed inspection item raises a complaint the same way any task does. **Rejected:** rebuilding it as task templates — it touches deposit deductions, exactly where a mistake costs real money.

### D62 — A failed item does not fail the task
Nine items fine and one problem reported means the task is **complete, with a problem recorded**. The failed item is what raises the complaint (D38) and what appears in "problems by room". **Why:** marking the task failed punishes the person for reporting a fault, which is precisely how you teach staff to tick everything fine — and then we lose both the fault and the trust.

### D63 — Required items must be answered before submitting
The submission will not go through with a required item blank; everything else stays skippable. **Why:** "required" has to mean something or the checklist is decoration, and a required photo that was skipped is exactly the evidence a dispute needs. This sits alongside the task-level "couldn't do it, here's why" outcome (F15a) — a person can report that the whole job was impossible, but cannot silently skip a required question.

## Reversals after stress-testing (D64–D65)

Both of these reverse earlier decisions. They are recorded with their reasoning so nobody re-derives the original position from scratch — which is exactly how we arrived at it the first time.

### D64 — Standing rules are deferred; nothing conditional ships in V1
> **Amended 2026-08-03 (adversarial review, D67–D79).** Two parts of this decision were revisited and changed.
>
> **F48, F49 and F50 are kept, reframed.** They were originally dropped with F6. But under D35 a rule *is* a recurring task, so listing, editing, pausing, archiving and seeing what a routine has produced is needed for **F5** whether or not conditions exist — otherwise a manager can switch a routine on and never inspect or stop it. F49's reach preview is if anything more useful for plain scope ("this will create 200 tasks every day") than for a condition. F48 is also where D78's "Not running — nobody assigned" guard lives.
>
> **Vacant-room readiness does get a home: F3 is pulled from V1.1 into this cycle.** The original loss was accepted on the grounds that no mechanism fitted. One does — a finished move-out creates the prep task directly, which needs no poller and no occupancy flag. Verified against the code: the move-out lock path exists and already raises complaints, so there is a seam to call from. **Correction (2026-08-04):** an earlier draft of this amendment said that path "reuses an open complaint rather than duplicating it." It does not. `moveOutChecklistService.ts:614` looks up complaints by `tenant_checklist_item_id` with **no status filter**, so it reuses open *and* closed ones — and the tenant-marked path does no lookup at all. **F54 therefore has no precedent to copy**; its open-complaint check is new work. The paragraph below is superseded.

**This supersedes D7, D33, D35, D36, D37, D41 and D50.**

We tested the five routines a condition was supposed to serve, and three of them were never conditional problems:

| Routine | What it actually is |
|---|---|
| Prepare a room before move-out | An **event** — notice gets given. Belongs to F3 (V1.1). |
| Clean empty rooms until filled | Also an **event** — a move-out completes, then the room is prepared. S2L's own data shows room prep is a 7–10 day job after a vacancy, not a daily sweep. |
| Re-visit slow complaints | **Already built.** The complaint module escalates on its own clock — 48h to L2, 72h to L3, configurable per property. A condition would rebuild production machinery. |
| Chase overdue rent | Genuinely conditional, but **mostly served**: the alert already lists who is overdue, and she turns it into work (D66) linked to each due. |
| Collect missing documents | Same shape as rent. |

**The argument that decided it:** we have no evidence for which conditions matter. We reasoned our way to five; we never watched an operator want one. Ship recurring work with real scope and a good template library, then watch which tasks operators keep re-editing the scope of — those are the ones that want a condition, and they will tell us with behaviour instead of our guesswork.

**Also corrected:** the registry analysis was used to argue *for* conditions, on the grounds that 21 alerts are "dead ends". Re-reading it, the missing capability was never conditions — it was that **an alert cannot become work**. A condition would only automate creating that work. See D66.

**What drops out:** F6 and prerequisite P2 (the stored room-occupancy flag, a schema change plus a derived value to keep correct forever).

**Superseded loss (kept for the record):** *"vacant-room readiness has no home in V1 — not a condition, not an alert, and F3 is V1.1."* No longer true; see the amendment above.

### D65 — A task shows the linked thing's status; it never suggests the work is done
**This supersedes D3 and most of D43.**

Suggest-close needed a hand-written rule for every kind of linked thing — paid for a due (but not partially paid, refunded or written off), a flag for KYC, a non-empty link for an agreement, a different flag for police verification, a date comparison for a warranty, one of 22 values for a complaint. Six kinds, six bespoke rules, each able to be wrong. Dues alone already needed three carve-outs. That is a growing pile of special cases, not a mechanism — and **a wrong "this looks done" is worse than no suggestion**, because it closes work that was never finished.

Instead the task **displays the current state of the thing it is linked to** and the person decides: *"Room 204 · ₹8,000 due · PAID, 2 Aug"*, or *"Partially paid, ₹3,000 of ₹8,000"*.

Why this is better on every axis: no per-entity rules, so a new kind of linked thing costs nothing · nothing can be wrongly suggested, because nothing is interpreted · it tells her more, not less · it explains the gap when the alert has dropped to eight while twelve tasks are still open · and it is honest — "your task shows the live state of the thing it is about" is defensible, where "it knows when you are done" was always going to be wrong sometimes.

If managers later ask for a nudge, we add it **only for dues**, where the rule is unambiguous — the same learn-first approach as D64.

### D66 — An alert can be turned into work, one task per item
The pending-task alerts are a live surface: they stay visible, update in real time, respect who can see them, and tap through to a filtered list. What they cannot do is become work with an owner and a record.

So an alert gets one action: **create work from this** — and it creates **one task per item**, not one task covering many. Twelve overdue dues become twelve tasks, each linked to its own due, all assigned in one action, and she can pick a subset from the filtered list.

**Rejected: one task linked to all twelve.** That needs multi-entity linking, which is deferred to V2 — and it makes closing ambiguous (if eight have paid, is it done?). One task per item avoids both, keeps each closure meaningful, mirrors how the alert count already falls as work is done, and produces a per-tenant attempt record that exists nowhere today.

It reuses bulk assign (F33b), linking (F8) and a template (F9) applied to a set the alert has already worked out. Without it she taps the alert, sees the twelve, then re-selects those same twelve by hand in the task module.

**Four rules that make it work, added by the adversarial review (D67–D79):**

1. **Assign from the list behind a card, never the card itself.** The card is a count that changes daily; a task must be a fixed thing you can prove you did. Tap the alert, tick the rows, assign those.
2. **Each registry entry is marked assignable or not**, one by one, not by category. "Tenants to install the app" is a visit; "WhatsApp balance low" is not.
3. **A row shows when it already has a task out** — "assigned to Ravi" on the row, "3 of 5 assigned" on the card — or she assigns the same thing twice within a week and stops trusting the alert.
4. **Handing over a task does not hand over the tenant's details.** The assignee sees the place and the action ("Room 204 — collect rent"), not the amount or the document history, unless they already hold that entity's permission. Dismissing a card never touches tasks created from it.

**Scope:** the assign path ships this cycle against the cards live today; growing the registry stays on issue #6249, phased. New cards inherit the ability as they ship. **Side effect:** F20 *is* the existing home feed with an assign action, not a new screen.

---

## Decisions from the 2026-08-03 adversarial review (D67–D79)

A hostile round-2 review produced 24 findings, worked one at a time with Sanchay. The grilling log — full argument, rejected alternative, and the three places the review was wrong — is at [review-round-2-decisions.md](review-round-2-decisions.md). Two of these correct earlier locked decisions (D13, D22); two were themselves corrected by a code check before landing.

### D67 — The band rule: a thing Band B would lie without is Band B
> **If a Band B feature would produce a false record without it, or would damage another part of RentOk, it belongs in Band B.**

Band C's definition ("the promise holds, but there are visible holes") had no room for items that make a Band B feature *untrue*, so five were misfiled there. The test is tight on purpose: not "would this be better with it", but "does the record become false, or does another module break." F14, F24a and F33b all fail it and stay in Band C — no comments is worse, not false.

**Five items move C → B:** **F53** (Ramu is permanently late, so F22 shows a failure that never happened) · **F15a** (blocked work and ignored work are identical in the record and in F21's numbers) · **F24c** (festival week records the whole team as failing, every year, permanently) · **F33a** (the guard going off at 10pm keeps open work that then goes overdue against him) · **F54** (*the second kind* — one leaking tap becomes seven live complaints and the queue Priya relies on becomes unusable).

**Accepted cost:** Band B grows by five. If B is over capacity, that trade is engineering's to surface, not a reason to misfile the items.

### D68 — The Brief stops calling the entity link ship-blocking; F8 moves to Band B
*(The "Open" question below is closed by D80 — F1 stays Band C, and the Brief's differentiator paragraph was removed rather than hedged.)*
The Brief's *"What has to ship for the bet to hold"* called one capability ship-blocking and bundled three claims into it; two of those sat in Band C and one (F6) is now cut — so the Brief and the requirements disagreed about what the cycle is for.

**The Brief was overclaiming, not the ranking.** What Priya misses on a Monday is the hour spent handing out work and not knowing afterwards whether it happened — *tell* and *prove*, which is what Band B already says. The entity link is what makes this ours rather than MaintainX's; that is a different sentence from "this has to ship."

**F8 moves C → B.** D45 already decided it (*"the 'every task ever on this room or tenant' history view gets built — it is what linking is for"*), so the ranking contradicted a locked decision. It is also the screen a dispute needs: the tenant says the room was filthy at move-in, Priya opens room 204 and shows nine months of dated proof. Without the screen the proof exists and nobody can find it — which protects nobody, which is the bet.

**The Brief's ship-blocking paragraph is rewritten** to match Band B, with differentiation moved to its own claim.

**F1 is not moot — it is the requirement that implements D65.** "A task shows the live state of the thing it is linked to" *is* show-status. It sits in Band C while the Brief presents it as one of the two things that make this ours. **Open:** either F1 moves to Band B, or the Brief says plainly that the differentiator is cuttable.

### D69 — Staff default to "see only my own"; D13 is corrected
**This corrects D13.** D13 said every existing user keeps the access they have today. But the task controller checks nothing today (Audit, Domain 3 — zero `checkAuthInDb` calls), so "today's access" means *everything*. F26 would have shipped as a permission model with every flag open for everybody — a data model, not a control — and nothing scheduled the tightening. That also made **D55 false in production on release day**: the back-door leaderboard D55 exists to prevent would be open from the first morning.

> Anyone who cannot create or assign work defaults to **"see only my own."** Everyone else keeps today's access.

**Why it locks nobody out:** "see only my own" still shows a person every task assigned to them — their entire job. The only thing removed is other people's work, which D55 already forbade.

**The migration's proxy — corrected after a code check.** The decision first claimed the rule "defines itself from the flags already on `team_member_property`." **Wrong** — there is not one task-related flag among that table's 94 columns. So the migration uses **`view_team` / `add_team` / `edit_team`**: anyone without them defaults to "see only my own." Managing the team is the closest existing signal for "hands out work." **Rejected:** `daily_ops` (broader than assigning work) and splitting by account role (a senior manager who is not an admin would lose visibility on day one).

**Ships in V1, inside M2, paired with M1.** It is a different value in a migration F26 already ships, not extra code; deferring means running the migration twice, and the second run *removes* access from people already using the module. D49's per-account enablement is the safety valve. **Accepted cost:** some week-one support calls, each answered by granting the assign permission.

### D70 — Per-person numbers come from fan-out, never from pooled
The review opened this as "pooled proof names people who were not there." **Sanchay narrowed it correctly: a missed pooled task carries no name at all** — nobody tapped, because nobody did it. So misses were never attributable and the review's example was wrong.

**The real exposure is the completed side.** F21 promises "each person their own number." For a pooled task that can only be a count of self-taps, which fails twice: it is a ranking of people built from self-declarations (the side door into what D15 and D22 forbid), and it is permanently half the picture — someone who works hard and does not tap looks idle with no way to prove otherwise, while tapping becomes the rewarded behaviour.

| Mode (D8) | Person on the record | Counted per person? |
|---|---|---|
| **Fan-out / single assignee** | Yes, before the work happens | **Yes — completions and misses.** Ravi's round is Ravi's whether he does it or not. |
| **Pooled** | Only after completion, by self-tap | **No — neither direction.** |

The self-stated name **stays visible on the individual task** — Priya needs "who do I ask about 204?" That is context; it stops being context the moment it is totalled. Room and property numbers are unaffected. A per-person miss count on fan-out is her working view, never a ranking, and the owner's screen keeps naming properties.

**D60's wording is corrected.** A missed pooled task has no owner at all, so "today no room has a recorded owner" is only half-fixed: a completed room records who did it; a **missed pooled room is the cleaning team's**, not a person's.

### D71 — The manager sees the same exceptions the owner sees; no head start
**This reduces D22.** D22 promised *"escalation reaches the owner only after she has had a fair chance to see it first"* and **no requirement implemented it.** F22 gave the founder his exception list, F25a his digest, F18 escalated — and Priya, the named top-3 adoption risk, got neither a head start nor sight of what the owner sees about her property.

**What ships:** she sees **the same exception list about her property that the owner sees about it** — same query, filtered to her property. She is never blindsided in a call and always knows what he is looking at. One screen, reusing F22.

**What does not ship:** the head start. There is no rule that his thresholds are later than hers. **D22 is therefore reworded** to what is built — *no surprises*, not *first look* — because leaving the original sentence in the source of truth repeats exactly the problem D68 fixes.

**Rejected:** an urgency override for critical failures — it needs a notion of critical checks that does not exist, and once there is an override she can never be sure which things bypass her.

**Noted, not fixed:** F22's shape still works against D22's spirit. "Sunshine PG: 6 rooms not cleaned in 3 days", newest first, across eight properties, read on a Sunday — the owner is counting how often each name appears. That is a ranking arrived at by inference. It is the honest cost of giving the owner anything at all.

### D72 — A property-wide audit is one task and one form; problems are free text
**The review was wrong here and was corrected.** It proposed repeatable checklist blocks and by-floor scope so a monthly building audit would not fan out into 200 tasks. Over-built. A monthly audit is **one walk, one form, one monthly report** — which is the shape S2L's own supervisor audit already takes, so it is observed behaviour, not a guess. It needs no new question type, no new scope branch, and no engineering.

**The shape:** one property-wide task, monthly. "All rooms clean? / Lift working? / Generator checked?" plus a free-text question listing any problems found, with photos.

**Accepted with the risk recorded:** problems are typed as free text, so a human still reads them and creates the tickets by hand. **This leaves S2L's stated top ask partly unmet** — their example was *"auditor marks 'Room 101 light broken', a human then has to create the ticket and assign it to Pankaj."* On a property-wide audit that human stays in the loop. **Rejected:** an "add a problem" picker (where / what / photo, pressed once per problem) that would have made each problem its own ticket via F2 and made "problems by room" countable in F21.

**Partial mitigation that already exists:** per-room checklists are unaffected. Daily room cleaning is one task per room, so a failed item there already knows its room and raises the ticket through F31 → F2. Only the property-wide audit loses the room.

**Open gap:** F2 assumes a complaint's location comes from the task's own location; nothing lets a problem name a place the task does not cover. Revisit if S2L complains about re-typing.

### D73 — Reminder model: four moments, all batched
The review's "real work has a window, not a deadline" half was **dropped**: setting the due time at the *end* of the acceptable window solves it with no build, and the only thing a real window adds is "not before X", whose two real cases are deferred anyway. **Guidance, not a field:** a due time means the end of the acceptable window, not the ideal moment; starter templates ship set up that way.

**What the round actually surfaced** — raised by Sanchay — is that nothing said *when* a reminder arrives. A message at the due time is a notification of failure. D24 had a daily summary, immediate messages and escalation, with nothing in between.

| Situation | What happens |
|---|---|
| Scheduled work due today | One morning message at the **property's send time**, carrying all links |
| Ad-hoc work | A message the moment it is created, whatever the due date |
| Has a due time, not done | **One nudge an hour before** — a count and one link, batched |
| Has a date but no time, not done | **One nudge at 6pm**, fixed — same shape |
| Self-task (F7) | Fires at the time the person set. No batch, no nudge, no escalation. |
| Late | Escalation, rate-capped, daytime (D24 unchanged) |

1. **The send time is per property, not per person.** Shift differences are handled by the manager setting a due time — there is no roster data (D34) and the system should not infer one.
2. **Everything batches.** 200 rooms due at 11am is one nudge per person, not 200, or the WhatsApp number is throttled at the first large property.
3. **The nudge carries a count and one link, never the task links.** The morning message is the delivery; the nudge is a poke. Repeating the links makes it a second morning message and people stop reading both.
4. **6pm is a constant, not a setting.** It works for day staff and for night staff starting at 10pm.
5. **Four message types is the ceiling.** D17 made WhatsApp the only channel with no fallback. Anything added later replaces one of these rather than joining them.

**Rejected:** 11pm for the no-time nudge (nobody does property work then, it reaches people asleep, and it is a failure notice with an hour left) · anchoring reminders to a task's start as well as its due (recurring work already appears in the morning message on both days) · per-checklist or per-property nudge lead times.

### D74 — Staff do not share phones; the persona claim is wrong
The review raised that F40/D24 promise "one message per person per day" while WhatsApp delivers to a *number*. **Sanchay's correction: the shared-phone use case does not exist in RentOk's customer base.** Staff have their own numbers. **No build** — no name-labelling, no grouping by number, no kiosk mode.

**This invalidates a claim carried in three docs**, all corrected: the **Brief's** persona section (*"share a cheap Android phone, often one between several"*), the **Audit's** cross-cutting list (*"shared devices — kiosk/quick-switch is the default deployment pattern"*), and **review-findings.md**, whose opening thread and strategic call #1 both rest on it.

**The weak connection is real** and everything built for it stands — offline partial save, photo compression, the 3-second cold-load gate. Kiosk and quick-switch stay in the v2 backlog as a **watch item**, not a known gap.

### D75 — Photos are deleted from the phone once uploaded
F46/D52 make photo questions camera-only with no exception (unchanged — one gallery upload makes every photo worthless). F30/D63 make a required item un-skippable. Together, a camera that will not open means a required-photo checklist cannot be submitted by any route — and the commonest cause of that on a cheap Android is a full phone, which camera captures landing in the gallery cause.

**One line in F39: once a photo has uploaded, the local copy is deleted.** The photo lives in RentOk, which is where the proof belongs.

D67 already ships the two escape hatches (F15a, F53). **Rejected:** a gallery fallback when the camera fails (the hole becomes permanent the moment it exists) and keeping local copies for a few days to allow retry (F12's partial save already holds unsent work).

### D76 — Translation is deferred; managers write in their own script
**This replaces F13 as written.** The review argued F13 belonged in Band A — an English checklist answered by a Hindi reader produces answers to questions the person did not understand, which is a false record, which is Band A's own test.

**The call: do not translate.** The manager writes tasks and her own checklists in her own script — Devanagari or any other vernacular. This is D32 applied unchanged — what is new is dropping app-level translation. It needs no engineering, and it covers most of what a cleaner reads.

**Accepted:** the app's own words stay English (Submit, Overdue, Approve) — friction rather than a wall, and this module should not be the first one translated if the rest of the manager app is not. Other regional languages defer with it.

**The one thing kept: RentOk's starter templates ship in Hindi as well as English.** F9 exists to remove the blank box and F47 has new properties start ready. English-only templates would force a Hindi-first manager to rewrite every one — the blank box with extra steps, in the feature built to prevent it. Writing the checklists twice is content work on templates being authored anyway.

### D77 — Duplicate tasks on the same thing are allowed
A uniqueness rule on task creation (one schedule + one period + one target = one task) was proposed to stop a scheduler retry producing a phantom "not done" record. **Rejected.** Duplicates created by a person are legitimate and must stay possible — a manager may deliberately create two tasks on the same room on the same day, and people close what they do not need. Recorded so nobody adds the constraint later.

### D78 — F47 and F25a move to Band C; routines are created unassigned
**F47 (new properties start with routines running) and F25a (the weekly WhatsApp digest to the owner) both move Band B → Band C.** Neither is the promise: F22 gives the owner his exception list and D71 gives Priya the same view of her property, so the digest is convenience on a screen that already exists.

**How a new property starts.** The objection to F47 was that a brand-new property has no staff, so routines fire into nobody and the customer's first week is a list of failures. Routing them to the admin was considered and rejected — a 60-bed property switching on daily cleaning gives the owner 60 tasks a day while he is still hiring, and the property accumulates a failure history before anyone existed to succeed, which then poisons F21 and F22 the moment he does hire.

**The landed answer:** routines are **created, enabled, and unassigned**. Nothing fires until the admin assigns someone, which he has to do anyway. He opens the app and sees his property already set up — F47's actual value.

**Corrected after a code check — this needs a scheduler change, not zero work.** The claim that unassigned schedules already produce nothing was **wrong**. `taskScheduler.ts` creates one task per member *only* when members exist and `system_purpose !== 'room_cleaning'`; otherwise it creates **one task with `team_member_id = undefined`**. So an unassigned routine does run. **The fix: the scheduler skips any schedule with no assignees.** Because `room_cleaning` is deliberately excluded from fan-out today (D60's shared link, visible in code), **M1 must land first** — which D69 already requires for its own reasons.

**One guard, inside F48, because "unassigned" is otherwise silent.** Ravi quits, Priya removes him from the cleaning routine, he was the last person on it, and cleaning stops silently for a week. F51 covers the person leaving; it does not cover a routine falling to zero people. So: a routine with nobody assigned displays **"Not running — nobody assigned"**, and removing the last person warns *"This will stop the routine. Continue?"*

### D79 — "Couldn't do it" is free text; no holiday list
**F15a's reason is free text.** **Rejected:** a short pick-list (*not home · refused · will do later · wrong person · no access*), which would have been countable — Priya seeing "not home ×8 this month" and switching to evening visits. Consequence accepted: she reads individual excuses and never learns the pattern. Consistent with D72, which chose free text on the same trade.

**No property-level holiday list.** F24c (skip a single occurrence) is Band B under D67, so a festival is a few taps. A holiday list is a new screen and a new setting for something that happens a handful of times a year. **The review raised it and then withdrew it.** Revisit only if a property running many routines complains.


### D80 — The moat is canonical sentence 8: the routines accumulate out of one person's head
**Decided 2026-08-04.** Settles the open moat question and F1's band.
*(Scope corrected by D81 — the mechanism here is right, the surface was drawn far too small. It is not routines accumulating; it is the property's whole work, across every role.)*

*(Two earlier answers — a capability moat, and "an alert becomes work" — were tried and discarded. The
argument is in [review-round-2-decisions.md](review-round-2-decisions.md).)*

**A moat is not decided by looking at competitors.** It is decided by what the product does for
the people it is built for. Stated as such:

> **A property runs on a system, not on one person's memory — so it survives the manager's
> absence and the staff churning.**

That is **canonical sentence 8**, already written and never called the moat.

**Why it is a moat and not a slogan:**

1. **It accumulates.** Each routine Priya sets up is a piece of her operating knowledge moved out
   of her head into the product. She built it, so nobody can hand it to a replacement — including
   a replacement product. Realistically six to eight routines at a PG, not dozens; the depth is
   modest and the mechanism is not.
2. **It pays out on the schedule this business actually runs on.** Today when a cleaner quits,
   Priya walks the new person around for days while things get missed. With the routines built,
   the new person opens the app and the work is there. At PG churn that lands every month.
3. **It is worth more here than almost anywhere.** Running on memory is fine when the same people
   are there next year. Here the people keep leaving and the property does not. Same mechanism,
   several times the value — which comes from who we build for, not from what anyone else built.

**The narrower, honest claim:** routines replace the **remembering and the daily assigning**, not
the skill transfer. A checklist says what and when; it does not teach a new cleaner how this
property wants a room cleaned. F30's reference picture on an item carries some of the "how" —
which makes F30 matter more than its placement suggests.

**What this changes:**

- **F9 is strategy, not convenience.** Nothing accumulates until routines exist, so the template
  library is the on-ramp to the only thing that makes anyone stay — the highest-leverage item in
  the set. **F47 (starter routines) is its cheap accelerant and stays in Band C** per D78; it makes
  the on-ramp faster, it is not the on-ramp.
- **F24c, F48, F49, F50 are moat defence.** They are the difference between six routines at month
  six and two. Not completeness items.
- **F30 rises with F9** — the reference picture is how a routine carries the "how".
- **F22 and F25a are sales, not moat.** They win the deal and justify the renewal. Real job,
  different job; they should not compete with the on-ramp for the same slot.
- **F1 stays Band C.** It is neither the moat nor the on-ramp — an ordinary convenience. This
  closes the question D68 left open by wrongly calling it "moot".
- **The Brief drops "none of them can do the things we can."** It points at the wrong thing and
  invites a comparison that is not the argument.

**The bet is the precondition for the moat, not a value beside it.** If Priya believes the module
is the owner watching her, she never builds the routines — and nothing accumulates. **D15, D22,
D69 and D71 therefore hold the moat up**, not a separate ethical position. The docs
previously carried these as two unrelated ideas.

**Accepted weakness: this moat is slow.** Nothing about it protects anyone in week one or month
two. It exists only once she has built enough routines to feel their absence. **Adoption is
therefore what buys the time for the moat to form** — the template library, the daily message
arriving when people can act on it, the runner loading in three seconds on a bad connection. That
is a better reason to care about those than "table stakes".

**Named dependency:** the day-one payoff assumes the new hire is already in the team list and can
read the runner. D74 left the app's own words in English; the task content is in the manager's own
script (D32, D74), so the questions are readable and the buttons are learned. Watch this if
onboarding a new staff member proves slower than expected.


### D81 — The module is the property's whole work, not its routines
**Decided 2026-08-04. Widens D80's surface; the mechanism is unchanged.**

D80 framed the moat as recurring routines accumulating out of the manager's head, and put the
number at "six to eight". **Both were too small.** That estimate came from imagining housekeeping
— i.e. from what the code does today — which let the current implementation set the ceiling. It is
the same error as building the moat out of competitor comparisons: looking at what exists instead
of at the user.

**The evidence was already in the vault.** The S2L dependency map (20 Jul 2026, ~50 buildings)
records the work they actually run:

| Role | Work |
|---|---|
| Caretaker | Daily — cleaning, CCTV, water tank level, WiFi, biometric access, plants, motor on/off. Weekly — terrace, tanks, balcony |
| Supervisor | Weekly audit **of the caretaker's work**. Monthly building audit — permits, fire NOC, utility NOC |
| Anyone visiting a property | **Visit form** — purpose, what was done, arrival and departure photos, timestamped |
| Property manager | Complaints; room-level splits between two managers |
| Ops lead | Reviews raw reports daily and directs the team on gaps |
| Building owner | Auto-emailed monthly audit report |

Six roles and roughly seventeen distinct jobs, at one account.

**What they have actually reached for — stated carefully, because the source doc's own verification
section corrects two errors that an earlier draft of this decision then repeated:**
- **One custom GPT is built and live** — a general room-inspection agent that walks a caretaker
  through a room question by question and holds the report until every mandatory item is filled.
  A **second** GPT, for move-out asset documentation, is **in design and not built**. Do not say
  "two custom GPTs".
- A **shared ChatGPT account across ~50 staff to log motor on/off is proposed, not committed**, and
  its feasibility is explicitly open in the source. A failed motor costs them ~₹20,000.
- The **visit form** and the **monthly owner report** are listed in the source under *ON US — RentOk
  build & ship*. They are things S2L wants, **not things they run today**.
- **"Google Forms" appears in no source.** It was invented by an earlier draft. Do not repeat it.

That is still a customer building their own operations tooling because none exists — the claim holds
on what is real, and does not need the parts that were not.

**Two lines from that map are live requirements evidence:**

> *"explicit room-level assignment between managers isn't currently possible in-app, so today's
> workaround is informal — two managers both see the same room list and split it manually, one
> starts from the top, the other from the bottom."*

> *"Shared 'dummy' manager account to be created — used only when one manager needs to help
> another complete checklists."*

**These are two separate things and must not be joined** — the source's verification section
explicitly corrects an earlier draft for exactly that conflation, and an earlier draft of this
decision made it again. The **room-split workaround** is evidence for F10 and F16. The **dummy
account** — future tense in the source, *"to be created"* — is for one manager helping another
complete checklists, which is evidence for proxy completion (F53, D30), not for assignment.

Either way, F10 and F16 are paying-customer pain today rather than speculation.

**The corrected story:**

> **A property's work is scattered across WhatsApp, paper, spreadsheets, whatever tooling the team
> has cobbled together, and one person's memory.** The module makes it one thing — every job, assigned to a named person, with proof,
> visible up the chain.**

The shape is not a checklist. It is **work → person → proof → visible upward** — which does not care
what the job is, which role does it, or how often it happens. So there is no natural ceiling on what
moves in. It also reframes the roadmap: the question stops being *"what feature next"* and becomes
**"what work is still outside the system"**, which is a question the customer can answer for us,
as S2L just did.

**The moat mechanism from D80 is unchanged and its surface is much larger:** the more kinds of
work move in, the harder the module is to leave, because there is no single thing to move back to
— they would have to return to five tools.

---

**The honest limit, tested against their own list.** "Anything becomes a task" is **not true
today**, and four of their work types prove it. Rather than build for all four:

| Their work | Call |
|---|---|
| Daily/weekly checklists · room-level splits · move-out asset docs | **Already covered** (F5, F10, F16, existing move-out) |
| Supervisor audits the caretaker's work | **Confirm, do not build.** He needs to see what the caretaker submitted — F8's history view should answer it. **F8 must state that history includes the submitted answers, not only that a task happened.** |
| Monthly compliance audit (fire NOC, permits, utility NOC) | **Already covered** — a monthly recurring task, per D70. S2L run theirs at least twice a month. |
| Tracking when a licence or certificate *expires* | **No build.** A renewal is a recurring task with a date the manager sets. No expiry tracking, no warning before it — say so rather than implying we track expiries. |
| **Visit log** — arrival photo, departure photo, time on site | **Build it, small.** See F59. Confirmed as a real pattern for any operator whose staff travel between buildings, not S2L-specific. |
| **Motor on/off log**, several times a day | **Explicitly out of scope.** This is telemetry, not work — a due date is the wrong shape for something logged four times a day. Naming it out is more useful than leaving it ambiguous. |

**Scope decision:** this widens what we *say*, not what we ship. The requirements were broadly
right; the story was too small. One small addition (F59), three decisions, no re-plan.

**Accepted risk — the horizontal trap.** "Anything can be a task" is exactly the framing that
makes a tool infinitely flexible and useless on day one, because nobody knows what to put in it.
The defence is unchanged and now matters more: **F9 is the on-ramp, F47 its accelerant** (D80). A blank
"create a task" box is the failure mode this story invites.


### D82 — The problem is late discovery, not lazy staff
**Decided 2026-08-04.** Sets how the problem is stated everywhere. Sharpens D80/D81 rather than changing them.

Earlier drafts described the problem as *"work is scattered across five tools"*. That is S2L's symptom
— they are sophisticated enough to have built workarounds. **Most operators have built nothing; things
simply do not happen and nobody finds out.** The problem is stated in the operator's own words instead:

**Layer one — the work fails in three ordinary ways.** Someone **forgets**. Someone **does it late**.
Someone **says it is done** when it is not. None of these needs a bad person; they are what happens when
there are more jobs than one head can hold and nothing keeps count.

**Layer two, and this is the expensive one — nobody finds out until it has turned into something else.**
The room is not cleaned Monday. Nobody knows Monday. The tenant complains Thursday, and it arrives as *a
complaint*, not a missed cleaning. He does not renew in March, and that arrives as *a vacancy*. The owner
learns about a ten-minute failure through a large slow consequence, months later, in a form that no longer
names the cause.

> **The person who reports on the work is the same person whose memory dropped it.**

That is why asking the manager for a better update does not fix it — the report carries the same blind
spot as the process. It is the strongest line in the argument and the reason "just get a daily update"
is not a competing solution.

**What we may honestly claim — this is a limit on language, not just a description:**

| Failure | The honest claim |
|---|---|
| Forgotten | **Prevented.** The work appears without anyone having to remember it. The only true prevention in the module. |
| Done late | **Not prevented — surfaced the same day** instead of next quarter. |
| Said done, was not | **Not prevented — made expensive.** A photo, a time and a place stop claimed work and real work looking identical. |

**"Block it" and "ensure it gets done" must not appear in any doc.** Nothing here forces a person to do
anything, and a doc that implies otherwise is found out by the first customer.

**The "they lie about it" framing is inverted, deliberately.** Pitched carelessly it is a watch-tool and
walks into Priya's documented fear. Stated correctly: **without a record, honest work and claimed work look
exactly the same — so the person who actually did it gets nothing for having done it.** A record exists so
an honest person can prove they are not the other one. Same mechanism, opposite owner; consistent with D15,
D22 and D71.

**Two features are reframed by this, not changed:**
- **F22 is not "the owner watches." It is "the owner finds out on day one."** Which is why D71 (the manager
  sees the same list at the same time) earns its keep commercially, not only ethically — nobody is being
  reported on.
- **F21's on-time rate is the leading indicator** — the number that moves *before* the complaint and the
  vacancy, rather than the one that explains them afterwards.

**Which failure leads the pitch, and why the order matters.** Forgetting and inefficiency are the manager's
pain; accountability is the owner's, and the owner pays. Leading with accountability wins the meeting and
confirms the manager's fear that the tool exists to catch her out — and she is the one who has to use it.
**So: forgetting first, accountability as the outcome.** The owner only gets real accountability if the
manager actually uses the thing, and she only uses it if it solves her forgetting first.

**Known weakness, recorded rather than hidden:** the cost chain — a missed cleaning becomes a complaint
becomes a vacancy — is currently a story, not evidence. If figures exist for how many complaints trace to
work that did not happen, or what an unready room costs per day, the argument becomes arithmetic instead of
persuasion. Worth getting.


### D83 — The cost chain is measured; the vacancy half of it is dropped
**Decided 2026-08-04.** Closes the known weakness recorded in D82. Live production query, 4 Aug 2026.

D82 recorded that the cost chain — a missed job becomes a complaint becomes a vacancy — was *"a story, not
evidence."* Both halves were tested.

**The complaint half holds, and is stronger than the story was.**

| Category group | Repeat within 7 days | Within 30 days |
|---|---|---|
| Maintenance (electrical, plumbing, power, carpentry, paint) | **33.4%** | 48.0% |
| Other | 32.0% | 41.8% |
| Cleaning + housekeeping | 20.4% | 30.3% |
| **All room-linked complaints** | **31.5%** | **44.1%** |

Base: 70,513 room-linked complaints over 12 months, test properties excluded. A "repeat" is a complaint on
the same room in the same category group as an earlier one. Cleaning and housekeeping together are 10.7% of
all ~120k complaints.

**What to say:** *about a third of the complaint queue is somebody chasing something that was already
reported and did not get done.* Quote the **7-day** figure; treat 30-day as an upper bound, because two
genuinely different faults can share a category.

**The vacancy half does not hold and is cut from every doc.** Median rent is ₹8,000/month (₹267/day). The gap
between a tenant leaving a room and the next joining is **18 days median *among rooms that refilled at all***;
across all vacancies there is no meaningful median, because 28% never refilled inside the year. The
distribution is what kills the argument:

| Gap before the room refills | Share |
|---|---|
| 0–7 days | 22.1% |
| 8–30 days | 22.7% |
| 31–90 days | 18.2% |
| 90+ days | 8.8% |
| **Never refilled within 12 months** | **28.1%** |

More than half of vacant rooms sit longer than a month and **28% never refill inside a year**. Those rooms
are not waiting for a cleaner, they are waiting for a tenant. Readiness binds only for the ~22% that refill
within a week.

**So "an unready room costs ₹267 a day" must not be written.** The first person to check would find that
most empty rooms have nothing to do with readiness — and would then doubt the complaint number, which is the
one that is real. **Recorded so nobody re-derives this argument in three months.**

**This does not weaken F3.** Making a room ready faster is still worth doing for the fifth of turnovers where
readiness is the constraint; it is the *portfolio-wide vacancy-cost* claim that fails, not the feature.

**Caveats.** Complaint categories come from `first_level` free text, which contains duplicate spellings
(`Internet` vs `Internet `, two forms of `Waterproofing & Paint`); the grouping is ours and does not move the
totals materially. Vacancy is measured room-level via `tenant.room`, not bed-level — in shared rooms this
*understates* the gap, so the real picture is no better than shown. `tenant.room` is the older structure;
a bed-level rerun via `tenant_room` would firm it up without changing the conclusion.

---

## What this supersedes

- The **"five outward write-backs"** framing in the v0 brief (preserved at `archive/task-module-brief-v0-original-2026-07-18.md`), and in the superseded PRD and pre-mortem (`archive/task-module-prd-SUPERSEDED.md`, `archive/task-module-pre-mortem-SUPERSEDED.md`). Tasks do not write back into dues/KYC/assets — see D2. Any doc that says a task "marks the invoice paid" or "marks the tenant verified" is superseded by this file.
- Any framing of the checklist template library as a **parallel workstream** — it is one feature requirement inside this redesign.

## Changelog of this changelog

- **2026-08-04 (b)** — Added **D80–D83**: the moat (canonical sentence 8, the work accumulating out of one
  person's head), the widened surface (the property's whole work, not its routines), the problem stated as
  late discovery rather than lazy staff, and the cost chain measured — the complaint half holds at 31.5%
  repeat-within-7-days, the vacancy half was tested and cut. D68 annotated.
- **2026-08-04** — Canonical sentences **1, 2, 3 and 9 rewritten**. They still described standing rules and suggest-close, both dropped by D64 and D65, so the sentences every other doc is told to copy exactly no longer matched the product. D1 retitled for the same reason. D3, D13, D22, D26, D33, D37, D41, D42, D43 and D60 annotated in place where a later decision reduced them. Added D67–D79.

- **2026-07-21** — Created as the source of truth. Locked 12 canonical sentences and 18 decisions (D1–D18) from the design conversation. Supersedes the outward-write-back model. (Product-lens review pending: a batch of behavior decisions D19+ to be added, and D16 to be reordered after D15.)
