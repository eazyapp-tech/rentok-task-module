---
title: "Task Module Redesign — Feature Requirements (one line each)"
date: 2026-07-21
owner: "Sanchay"
status: "current"
tags: [rentok, tasks, requirements]
---

# Task Module Redesign — Feature Requirements

Every requirement, one line. This is the scannable master list and the canonical F-numbering — the [PRD](Task%20Module%20PRD.md) elaborates each F#, the [workflow specs](workflows/) show each flow, the [CHANGELOG](CHANGELOG.md) holds the decisions (D#) behind them. Tags: **[cycle]** this cycle · **[next]** next sprint · **[later]** a named later phase · **[v2]** backlog.

## The moat

- **F1** — A task tied to a real thing reads that thing's state and *suggests* it is done; the person confirms (a due paid → suggest close). **[cycle]**
- **F2** — A failed check or inspection notifies the manager and hands them a complaint with the issue and asset details pre-filled; the person raises it, linked to the task, statuses independent. **[cycle]**
- **F3** — The business creates tasks on its own from its events (move-out notice → deposit-inspection task). **[next]**

## Creating work

- **F4** — Create a one-off task at any time, assign it, set a due date and time. **[cycle]**
- **F5** — Create a recurring task on a configurable cadence (daily / weekly / monthly), starting any time — not frozen to one frequency. **[cycle]**
- **F6** — Set a standing rule: a curated trigger + a cadence + a stop condition, recommended and shipped ready to switch on. **[cycle]**
- **F7** — Keep a self-to-do or log for yourself with a reminder; set recurring routines for yourself or the people you lead. **[cycle]**
- **F8** — Link a task to a real entity (due, tenant, room, asset) for context, filtering, navigation, and history. **[cycle]**
- **F9** — Build tasks from a checklist template library; RentOk recommends the templates a property like yours runs. **[cycle]**
- **F10** — Scope a task or rule to the whole property, a floor, a room, or a hand-picked set. **[cycle]**

## Doing the work

- **F11** — Do a task in the runner with proof — location, time, photo, signature — owned by the staff member first. **[cycle]**
- **F12** — Partial work survives a tab close, a network drop, and a phone restart (held on the phone this cycle). **[cycle]**
- **F13** — Hindi plus at least one regional language across the runner UI *and* the template content and notifications a staff member sees — not just the app chrome. **[cycle]**
- **F14** — A comment thread on a task with @mention tagging and notify. **[cycle]**
- **F15** — A "couldn't do it" outcome with a reason, plus voice notes for staff who cannot type. **[cycle]**
- **F16** — Assign a task to several people two ways: pooled (any one completes it, closes for all) or fan-out (each completes their own copy); the system records who did each. **[cycle]**
- **F17** — Each staff member's own list of today's tasks and their record of what they completed. **[cycle]**

## Time pressure

- **F18** — Every task carries a due date, turns overdue when it passes, sends reminders as the date nears, and escalates to the manager and then the owner when ignored. **[cycle]**

## Review, oversight, and insight

- **F19** — Review loop: approve, reject with a reason, or send back for rework. **[cycle]**
- **F20** — The manager's unified list: the three task sources in one category-filtered place. **[cycle]**
- **F21** — First insight cut: completion by staff, on-time rate, failure by room, week-over-week trend. **[cycle]**
- **F22** — Founder oversight: which properties are keeping to the standard, across all of them at once. **[cycle]**
- **F23** — Shift handover: carry open tasks to the next person. **[cycle]**
- **F24** — Quick-capture an ad-hoc task; approve on mobile; skip or reschedule a single instance. **[cycle]**
- **F25** — Owner weekly WhatsApp digest; audit export of the task record. **[cycle]**

## Foundation and data integrity

- **F26** — Access control across the module; close the currently-open runner/trigger routes; the migration defaults every existing user to the access they have today. **[cycle]**
- **F27** — Task and rule creation built as a callable action, so the assistant can create by talking later. **[cycle]**
- **F36** — Validate every submission on the server against the checklist it belongs to; never store unvalidated responses. This is a proof tool — the record has to be trustworthy. **[cycle]**
- **F37** — An edit log on every task (who changed what, when) and a lock after submit, so the proof cannot be quietly altered after the fact. **[cycle]**
- **F38** — Archive and restore tasks and templates instead of hard-deleting them, so nothing is lost by accident. **[cycle]**
- **F39** — Compress photos on the phone before upload, so proof survives a 2G connection (pairs with the runner ship gate). **[cycle]**
- **F40** — Notifications go WhatsApp-first — reminders, escalation, @mentions, the owner digest — and the staff's own list (F17) and the manager's list (F20) are the in-app inbox. Push and email are a named later decision, not an accidental hole (see [CHANGELOG](CHANGELOG.md) D17). **[cycle]**

## Horizon

- **F28** — Build tasks and standing rules by talking to RentOk's assistant, grounded in the real property — the power-and-accessibility door. **[later]**

## The checklist itself — question types and items

Today's builder supports five field types (yes/no, text, number, single-select, photo). A top-1% ops/inspection checklist needs more.

- **F29** — Expand the question types beyond today's five: add **rating / scale** (quality scoring), **pass / fail / N-A** (inspections need "not applicable"), **multi-select**, **multiple photos with an annotation**, **voice note** (for staff who cannot type), **date / time**, and **measurement with a unit** (a meter reading), plus a non-input **instruction block** that shows guidance or a "what good looks like" reference image. **[cycle]**
- **F30** — Per item: mark it required, require a photo or proof on it, attach a reference image, group items into sections, add a note, or skip it with a reason. **[cycle]**
- **F31** — A single item can fail on its own and hand over the pre-filled complaint (F2) — not only the whole task. **[cycle]**
- **F32** — Give a task a category, a priority, a description, and — for a recurring task — an optional end date. (A category is required for the unified category-filtered list, F20.) **[cycle]**
- **F33** — Bulk-assign one task across many rooms or staff at once; reassign an in-flight task to someone else. **[cycle]**
- **F34** — Score a checklist from its ratings (a quality score) so a standard can be measured over time. **[v2]**
- **F35** — Conditional items — show an item only when a prior answer warrants it; and scan an asset's barcode / QR tag as an answer. **[v2]**

---

## Deferred, next, and v2 — preserved and tracked (nothing dropped)

Everything scoped out of this cycle, kept so it is not lost:

- **F3** — event-driven task creation. **[next sprint]**
- **F28** — the assistant ("build by talking"). **[later]**
- **Full system pending-tasks registry** — build Type-1 out from ~11 hardcoded to the full 65-entry registry (categories, T1–T5 tiers, config table, role filtering, ranking). **[later — GitHub issue eazyapp-tech/rentok-backend#6249]**
- **Server-side draft** for partial save (beyond the client-side one). **[fast-follow — D12]**
- **Custom cadences** — every-N-days, specific weekdays (needs an enum + scheduler change). **[v2 — D16]**
- **Checklist scoring (F34)** and **conditional items + barcode/QR scan (F35)** — checklist-content power beyond the essential types. **[v2]**
- **Push notifications, email, dedicated in-app inbox** — WhatsApp-first this cycle (F40); these are competitor table-stakes deferred by decision, not omission. **[v2 — D17]**
- **One task linked to many entities of different types** (junction table) — F8 links a single entity; the true-multi case waits until a real scenario commits it. **[v2]**
- **Advanced review** — multi-step approval chains, SLA-on-review, auto-approve, bulk-approve, conditional review. **[v2]**
- **Advanced assignment** — delegation, out-of-office routing, capacity/load-balancing, external vendor assignees. **[v2]**
- **Advanced escalation** — quiet hours, auto-close, auto-reopen. **[v2]**
- **Request/ticket and work-order task kinds** (tenant- or staff-initiated inbound; cost/parts/vendor). **[v2]**
- **Shared-device kiosk / quick-switch mode** — higher value than a typical v2 item since shared devices are the default deployment; revisit early. **[v2]**
- **T6 proactive tasks** — 48 candidate system-detected tasks that grow the registry from ~102 to ~147. **[v2 — vault: `RentOk/Product/T6 Proactive Tasks — v2 Candidates.md`]**
- **Audit roadmap** — the differentiator `[D]` capabilities scored 0 and the innovation `[I]` capabilities from the Feature Gap Audit. **[v2 — `feature-gap-audit.md`]**
- **The visual "top 1%" redesign (v2 web baseline)** — the UI/UX overhaul of the Runner and manager surfaces. Tracked separately on the `module-redesign-pipeline` track; this doc set is the product/backend scope it builds on, not the visual redesign itself.

## Prior work & research — preserved, not lost

Where the accumulated work lives, so nothing from earlier sessions is orphaned:

- **Detailed brief** (all the long-form richness): `archive/task-module-brief-detailed.md`, and the original working draft in the `rentok-checklist-library` repo (PR #6).
- **Engineer evidence:** `Task Module - Feature Gap Audit.md` — 15 domains, ~250 capabilities scored against the code.
- **Vault research:** `RentOk/Product/pending-tasks-registry.md` (65 tasks), `T6 Proactive Tasks — v2 Candidates.md` (48), the COMP-0xx task/complaint rules, and the Persona Bible (`icp_and_personas.md`).
- **This session's grounding** (destination surfaces for write-backs; the announcement/survey filter + trigger mechanics; the room-cleaning frequency finding; the comment/@mention reuse) → being folded into the Audit and the workflow specs' Engineering Notes so it is captured in the doc set, not only in conversation.
- **Checklist library sub-project** (feature F9's detailed docs + template content): the `rentok-checklist-library` repo.

---

**Ship gates (hard — a requirement that misses these does not ship):** the runner cold-loads under 3 seconds on a 2G connection with no stored files, and partial work survives a tab close / network drop / phone restart (F11, F12); the runner ships in Hindi plus one regional language at launch (F13).

**Not this cycle or next:** fines, salary deductions, a staff scorecard ([CHANGELOG](CHANGELOG.md) D15); a free-form rule builder (D7); attendance/shift-clocking; a native Task tab (D11); rebuilding move-out (D14).
