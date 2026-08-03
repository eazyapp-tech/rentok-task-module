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

## What this supersedes

- The **"five outward write-backs"** framing in the v0 brief (preserved at `archive/task-module-brief-v0-original-2026-07-18.md`), and in the superseded PRD and pre-mortem (`archive/task-module-prd-SUPERSEDED.md`, `archive/task-module-pre-mortem-SUPERSEDED.md`). Tasks do not write back into dues/KYC/assets — see D2. Any doc that says a task "marks the invoice paid" or "marks the tenant verified" is superseded by this file.
- Any framing of the checklist template library as a **parallel workstream** — it is one feature requirement inside this redesign.

## Changelog of this changelog

- **2026-07-21** — Created as the source of truth. Locked 12 canonical sentences and 18 decisions (D1–D18) from the design conversation. Supersedes the outward-write-back model. (Product-lens review pending: a batch of behavior decisions D19+ to be added, and D16 to be reordered after D15.)
