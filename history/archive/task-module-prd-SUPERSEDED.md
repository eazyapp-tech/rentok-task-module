---
title: "PRD: Task Module Redesign (SUPERSEDED)"
date: 2026-07-20
version: "1.0"
status: "superseded"
owner: "Sanchay"
tags: [rentok, prd, tasks, superseded]
---

> # ⛔ SUPERSEDED — DO NOT BUILD FROM THIS
>
> **Kept for its structure and history only.** This PRD is wrong at the model layer, not merely out of date.
>
> **What is wrong in it:** it describes tasks *writing back* into other modules — "marks the invoice paid", "marked verified", "five write-backs" as the moat. That direction is incorrect. A task **reads** the state of the thing it is linked to and **suggests**; it never writes into it. It also links tasks to a KYC *state* (a task links to a **tenant**), treats move-out deductions as something to build (that already exists — reuse it), and states "rent overdue → no collection task appears" (that is an existing system-raised task, a different kind).
>
> **Its F-numbers collide.** This doc uses its own F1–F13 scheme in which F8 is the template library. The canonical scheme is **F1–F40** in `../feature-requirements.md`, where F8 is entity linking and F9 is the library. Never cite an F-number from this file.
>
> **Read instead:** `../CHANGELOG.md` (the source of truth — canonical sentences + decisions D1–D18), `../feature-requirements.md` (F1–F40), `../Task Module Brief.md`.
>
> A replacement PRD is to be written from the CHANGELOG and the canonical F-list.

# PRD: Task Module Redesign

> *Link targets on the next line edited 2026-09-05 (files moved); wording untouched.*
> Read the [Brief](../../01-brief.md) first — it holds the bet. This PRD holds the requirements. The [Feature Gap Audit](../../reference/feature-gap-audit.md) holds the code-level evidence behind every "today it does / does not" claim here. The risk analysis lives in the [pre-mortem](task-module-pre-mortem-SUPERSEDED.md).
>
> Draft · Owner Sanchay · Nimit and Jatin review the two open schema questions (see Open Questions).

## TL;DR

The Task module today runs a scheduled checklist and stops there. This redesign turns it into the tool a property's staff use to prove their work — where the proof protects the person who collected it, not the owner who watches. A finished task writes its result back into the module that owns the outcome: rent gets marked collected, a tenant gets marked verified, move-out deductions get recorded. A failed task creates the next action instead of dying silently. Around that, the module finally gets what every serious operations tool already has and ours does not — due dates, a review loop, real proof of work, the staff member's own record, and access control.

**The moat is two builds.** First, finished tasks writing back into rent, deposits, complaints, verification, and assets — ship-blocking this cycle. Second, the rest of RentOk telling the task module when something happened, so the right task appears on its own — the sprint after. Together they are the one thing no competitor can copy, because only RentOk owns the property the work is about.

**The bet is a build constraint.** No fines, no deductions, no staff scorecard — this cycle or next. The moment staff believe the tool can cost them pay, they stop filling it honestly and the proof collapses for everyone.

## Problem Statement

### What's broken

A property runs on daily provable work: rooms cleaned, meters read, KYC collected, move-out inspections done, deposits settled, complaints closed. The manager's real problem is not knowing the work exists — it is knowing it happened, by whom, and what to do when it did not.

The module was built for one shape of that: schedule a checklist, a staff member fills it in. It stops there. Today:

1. When a cleaning check fails, nothing happens — no complaint, no follow-up, no record anyone noticed.
2. When a payment is collected on a task, the invoice it was for stays marked unpaid.
3. When rent goes overdue, no collection task appears.
4. Tasks carry no due date, so nothing is ever late and nothing chases itself.
5. There is no review — a manager cannot approve, reject with a reason, or send work back.
6. The module has no access control of its own; the runner that staff submit through is open, and the controller checks no permissions.

So managers keep the real system in their head, on WhatsApp, and in a paper register. The module holds a copy of the checklist but not the truth of whether the work happened or what it changed.

### Who it affects

**Primary:** the on-site manager and the staff who do the physical work. The manager is running the property blind to whether her assignments landed; the staff have no record that protects them when a tenant complains after the fact.

**Secondary:** the off-site owner, who wants the exceptions without reading a hundred rows, and cannot get them because the module does not know which work failed.

### Root cause

The module was built as a checklist runner. A checklist runner records answers. It has no place to put the knowledge that a failed answer should raise a complaint, that a collected payment should clear an invoice, that a move-out answer should deduct from a deposit, or that a person needs permission to see a task. Every gap in this PRD follows from that one starting point. The fix is not more checklist features — it is giving the module two connections it never had: writing a finished task back into the module that owns the outcome, and receiving events from the rest of RentOk.

## Personas

Sourced from RentOk's Persona Bible (`icp_and_personas.md`). These are this module's grounded personas — note that "Priya" here is the on-site **manager**, not the multi-property owner of the same name in the general RentOk persona set.

### Priya — On-Site Manager (Primary)

Runs the property day to day. Assigns the cleaning, chases the KYC, handles the angry tenant at 4pm. Reads Hindi more comfortably than English. Her stated fear (Persona Bible L178, L234): that this becomes the owner's way of catching her out. A manager who feels watched quietly kills adoption for everyone under her.

Today: runs the property from her head, WhatsApp, and a register, because the module can't tell her what actually happened.

After: her daily task list lives inside the app she already opens; failed work raises its own complaint; she approves, rejects, or sends work back from her phone.

### Housekeeping & Maintenance Staff (Primary)

Do the physical work. Change jobs often. Share a cheap Android phone — sometimes one between several people — on a weak connection. They are who the proof is collected *from*.

Today: fill a checklist that helps the owner, not them, and lose their work when the network drops mid-task.

After: complete tasks with proof that survives a dead network, and keep their own record of what they did — their defense, not the owner's accusation.

### Ramu — Security Guard (Secondary)

Mans the gate, logs entries and exits, does night rounds. His paper register is his job and his dignity (Persona Bible L192).

After: does his rounds and gate log as tasks positioned as the modern tool that makes his job respected, not the app that replaces him.

### The Off-Site Owner (Secondary)

Runs several properties, rarely on any one. Pays for RentOk. Wants the exceptions, not the rows.

After: opens an exception view that tells him what did not happen and what is at risk, without asking anyone.

## North Star

**Product (user voice):** "My staff fill the forms because the record helps them, and I run my property from the module instead of my head."

**Feature (one measurable proxy):** staff task-completion rate on live properties — the honest test of whether the anti-surveillance bet holds.
- **Counter-metric:** share of tasks submitted with real proof (location, time, signature) versus empty submissions. A high completion rate with hollow proof means staff are ticking boxes, not doing work.
- **Supporting:** failed-task-to-complaint conversion (does a fail actually produce an action), and manager approval turnaround.

**Company North Stars this maps to:** Digital Compliance Coverage (% of operations run through the app), Ops Time Saved (hours/week), and — through the write-backs into rent and deposits — Net Rent Realized.

## Goals and Non-Goals

### Goals (this cycle)

1. A finished task writes its result into the module that owns the outcome: a collected payment marks the invoice paid, a completed KYC step marks the tenant verified, a move-out inspection records the deposit deduction, an asset check records the item's condition.
2. A failed task creates a corrective action instead of ending silently — including raising a complaint in the property's complaint list when the failure is a complaint-worthy one.
3. Every task carries a due date, turns overdue when the date passes, sends reminders, and escalates when ignored.
4. A manager can review a submitted task end to end: approve it, reject it with a reason, or send it back for rework.
5. Staff collect proof as part of the work — location, time, signature — and that proof, and any partial work, survives a tab close, a network drop, and a phone restart.
6. Every staff member has their own view of their tasks and their own record of what they completed.
7. The manager's daily task list appears inside the mobile app she already uses, reached through the existing web view rather than a new tab.
8. A manager can set up the right tasks from a template library instead of a blank box, with recommendations for what a property like hers usually needs.
9. The module has access control: the right people see and act on the right tasks, and no task endpoint is open. The change defaults every existing user to keep the access they have today, so nothing they can do now disappears.
10. A manager can see what is failing and where — completion by staff, failure by room, week-over-week trend.
11. Staff and wardens can hand a shift over to the next person, carrying open tasks with it.
12. A manager can approve on mobile, raise an ad-hoc task on the spot, and skip or reschedule a single task instance without breaking its schedule.
13. Owners get a weekly WhatsApp digest, and a manager can export the task record for audit.
14. The runner works in Hindi and at least one regional language at launch.

### Ship gates (hard — a goal that misses these does not ship)

- **Runner speed and durability.** The staff runner cold-loads in under 3 seconds on a 2G connection with no stored files, and partial work survives a tab close, a network drop, and a phone restart. "Fast and offline-tolerant" without these numbers is not a requirement — if it misses, Goal 5 does not ship.
- **Language.** The runner ships in Hindi plus at least one regional language. This is a launch requirement, not a fast-follow — Priya and Ramu do not read English comfortably.

### Non-Goals (this cycle)

- **Fines, salary deductions, or a staff scorecard.** The bet forbids it — staff who feel the tool can cost them money stop filling it honestly. Not this cycle or next.
- **Attendance and shift-clocking.** A different product; folding it in blurs what this module is for. Wardens still hand over shifts (Goal 11), but the module does not clock hours.
- **AI-generated checklists.** The template library gives managers real, reviewed content. A generator is a later question, not a launch need.
- **Rebuilding move-out.** The move-out inspection already records deposit deductions correctly and turns them into invoices and expenses. This redesign connects to it; it does not touch it.
- **A native Task tab in the mobile app.** The runner and the manager's list reach the app through the existing web view. Cheaper to ship, and it keeps the app the manager already knows.
- **One task covering many properties or many entities at once.** A task is about one thing this cycle. The wider case waits until a real property needs it.

### Deferred to the next sprint (named, not dropped)

- **The event layer (F2).** The rest of RentOk telling the task module when something happened — rent overdue, refund processed, move-out complete — so the right task appears on its own. This is the second half of the moat. It is a larger change across many parts of RentOk, scoped and scheduled for the sprint right after this one.

## Feature Requirements

### F1 — Finished tasks write back into the business (the moat, part one)

This is the capability no competitor can match, and it is ship-blocking this cycle.

Today a submitted task only stores its answers. It does not touch the thing it was about. F1 adds one step after a task is submitted: the result is written into the module that owns the outcome. Five write-backs ship this cycle:

| When a task... | The result is... | Lands in |
|---|---|---|
| Collects a rent or bill payment | Recorded as a payment against that invoice | The dues and collections list — marked collected |
| Completes a tenant's KYC | The tenant marked verified | The tenant's profile and the KYC-pending counts |
| Records a move-out inspection | The deposit deduction recorded on the existing inspection | The move-out checklist, which already turns it into an invoice or expense |
| Checks an asset | The item's condition recorded | The asset's record |
| **Fails** a check that is complaint-worthy | A complaint raised, linked to the task | The property's complaint list — see F1a |

Two rules govern every write-back:

- **A write-back uses the module's own front door, never a shortcut.** Marking rent collected goes through the same payment path a manual collection uses — so receipts, late-fine rules, and the money record all stay correct. A task never quietly sets a "paid" flag on its own. The same holds for every target: the write lands where that module already expects it, or it does not land.
- **Two write-backs have a known constraint to resolve, not design around.** "Asset condition" has no dedicated home today — RentOk tracks assets as inventory, which holds a status and a damage cost but no condition field, so this write-back uses the inventory record or the move-out inspection's condition, and does not invent a new place. This is flagged for the build, not hidden.

#### F1a — A failed task creates the next action

A failed check does not end the task — it starts the next one. Depending on the failure, the corrective action is either a follow-up task on the same staff member or a complaint raised in the property's complaint list. A raised complaint is linked to the task that raised it and enters the exact escalation path every other complaint follows — the same reminders, the same tiers, the same manager view. It does not become a second, parallel kind of complaint. The manager sees it in the one complaint list she already checks.

### F2 — The business tells the task module what happened (the moat, part two — next sprint)

The reverse of F1. Today the only way a task appears is a manager scheduling it. F2 lets the rest of RentOk create tasks on its own: rent goes overdue and a collection task appears; a move-out completes and the deposit checklist appears; a complaint is reassigned and the new owner gets the task.

This is a larger change — it touches the parts of RentOk that handle invoices, refunds, complaints, and move-outs, and some of those are harder to hook into than others. It is named, scoped, and scheduled for the sprint right after this cycle. It is not in this cycle's build. The Brief and the Audit both explain why it and F1 are one outcome but two builds.

### F3 — Due dates, overdue, reminders, escalation

Every task gets a due date. When the date passes, the task turns overdue. Overdue tasks send reminders and, if still ignored, escalate to the manager and then the owner — following the same tier model RentOk's complaints already use, so the behavior is one a manager recognizes, not a new one to learn. Reminders go out over WhatsApp first, the channel staff and managers actually read.

### F4 — The review loop

A submitted task is not automatically done. A manager can:

- **Approve** it — the work is accepted and any write-back from F1 takes effect.
- **Reject** it with a reason — the staff member sees why.
- **Send it back** for rework — the task returns to the staff member's list with the manager's note.

Approve is a superset of submit: approving includes everything submitting recorded, plus the manager's acceptance. The move-out inspection already works this way, so this follows a pattern the team has shipped before.

### F5 — Proof of work that survives a bad network

Staff collect proof as part of doing the task: where they were (location), when (time), and a signature where the work needs one. Two hard requirements:

- **Partial work is never lost.** A staff member half-way through a task who closes the tab, loses signal, or restarts the phone comes back to their work intact. This cycle, the runner keeps that partial work on the phone itself and submits it when the network returns. A server-side draft is a fast-follow, not this cycle.
- **The proof is honest.** Location and time are captured as the work happens, not typed in after. This is what makes the proof worth trusting — for the manager who relies on it and the staff member it protects.

### F6 — The staff runner and the staff record (the anti-surveillance move)

Each staff member has one view: their tasks today, and their record of what they have completed. This is the feature that makes the bet real. The same photo and timestamp that tell the owner the work was done are, first, the staff member's own record that they did it. When a tenant complains at 4pm about a room cleaned at 9am, the staff member has their proof. The owner sees the exception; the staff member sees their record. Same data, different owner — and the staff member's ownership comes first.

The runner is the trust-critical part of the whole module and the least forgiving to build: shared cheap phones, weak signal, Hindi and regional languages. It is governed by the ship gates above.

### F7 — The manager's daily task list, inside the app

The manager's tasks appear inside the mobile app she already opens, in the daily list she already checks — reached through the existing web view, not a new tab. The redesign reuses the words and the shape of RentOk's existing pending-work list where they fit, and does not copy the parts built for money tasks that do not apply to physical work.

### F8 — The checklist template library (one requirement inside this redesign)

A manager should not face a blank box when setting up tasks. F8 gives her a library of ready checklist templates and recommends the ones a property like hers usually needs — the cleaning checklist, the move-out inspection, the meter reading round. She picks, edits if she wants, and schedules.

This is **one feature requirement inside this redesign**, not a separate project. It solves the setup half of the module: without content, a redesigned engine still starts empty. The library work has its own detailed documents, but it ships as part of this redesign and is measured as part of it.

### F9 — Access control across the module

Today the module has none of its own: the runner staff submit through is open, and the controller checks no permissions. F9 builds it. The right people see and act on the right tasks, keyed on the same per-property permission model the rest of RentOk uses.

The one rule that protects the rollout: **the change defaults every existing user to the access they have today.** Nobody loses a task they can currently see on the day this ships. Access gets tighter deliberately later, per property, not silently on release.

### F10 — First insight

A manager can see the shape of the work, not just the list: completion by staff member, failure by room, and week-over-week trend. This is the first cut — enough to answer "who is keeping up and where is it breaking," not a full analytics build.

### F11 — Shift handover

Wardens and staff work in shifts. F11 lets one person hand over to the next, carrying the open tasks with them, so work does not fall in the gap between shifts. This is the spine for multi-shift properties.

### F12 — Manager quick-capture, mobile approval, single-instance changes

Three smaller manager abilities that round out the daily loop:

- **Quick-capture:** raise an ad-hoc task on the spot when the manager spots an issue.
- **Mobile approval:** run the F4 review loop from the phone.
- **Skip or reschedule one instance:** move or skip a single occurrence of a scheduled task without disturbing the schedule itself.

### F13 — Owner digest and audit export

- **Weekly WhatsApp digest** to owners: what got done, what failed, what is at risk — the exceptions, not the rows.
- **Audit export:** a manager can export the task record (who did what, when, with what proof) for a compliance or dispute need.

## Cross-Cutting Behavior

- **Notifications:** WhatsApp first, then the app. Reminders, escalations, and the owner digest all lead with WhatsApp because that is what this audience reads.
- **Permissions:** surfaces are hidden when a user lacks access, never shown greyed-out. Owners and admins see everything.
- **Language:** the runner and staff-facing text ship in Hindi and at least one regional language at launch. Manager and owner text follow RentOk's existing language support.
- **Offline:** the staff runner reads and holds partial work offline and submits on reconnect (F5). Manager review and setup need a connection.
- **Audit:** every task write-back and every review action records who did it and when. A write-back never destroys the record it writes into — it adds to it, the same way a correction entry does elsewhere in RentOk.
- **Time:** stored in UTC, shown in IST. "Today" is IST midnight to midnight, matching the rest of the app.

## Open Questions (owners assigned, answers gate the build)

1. **Template storage (Nimit).** Does the template library extend the existing task template, or get its own store? This decides how F8 is built. Answer before the library build starts.
2. **Schema change path (Jatin).** The write-backs and new task states need schema changes, and RentOk has no migration runner — changes go through hand-written scripts. Which path this redesign uses is Jatin's call. Answer before F1 build starts.

Both are engineering-shape questions, not product questions — the product need is settled in this PRD. They are tracked as GitHub issues on the backend repo.

## Changelog

- **2026-07-20** — First PRD for the Task module redesign. Written from the Brief (the bet) and the Feature Gap Audit (the evidence), with destination-surface grounding for all five write-backs. Frames the redesign as the parent scope with the checklist template library as one feature requirement (F8) inside it. Moat split into F1 (this cycle) and F2 (next sprint). Code-level detail kept out of the body and left in the Audit.
