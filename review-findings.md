---
title: "Task Module — Product-Lens Review Findings (2026-07-21)"
date: 2026-07-21
owner: "Sanchay"
status: "open — 7 decisions pending"
tags: [rentok, tasks, review, risk]
---

# Product-Lens Review — Findings

Three independent reviewers critiqued [feature-requirements.md](feature-requirements.md) against the [CHANGELOG](CHANGELOG.md) and the [Brief](Task%20Module%20Brief.md): one on **flow completeness**, one **user-first**, one on **operational edge cases**. ~30 findings, deduped below.

**Nothing here is applied yet.** The 7 strategic calls need a decision; the behavior decisions need to be written into the CHANGELOG as D19+ before the spec layer is built.

---

## The one thread behind half of it

The persona says staff share **a cheap phone, one between several, on a weak connection**. The requirements solved the *weak-connection* half with rigor — offline partial save, photo compression, the 3-second 2G cold-load gate — and **ignored the shared-device half** (kiosk / quick-switch sits in the v2 backlog).

That single omission silently voids **F11** (proof "owned by the staff member"), **F16** (records who did it), **F17** ("their own record") and the bet itself: a record cannot protect a person if the phone does not know who is holding it. Shared-device identity is a **precondition** of three headline features, not a v2 nicety.

---

## The 7 strategic calls (pending a decision)

Recommendation on each is **yes**.

| # | Call | Why |
|---|---|---|
| 1 | **Pull shared-device actor-select into this cycle** as a ship gate for F11/F16/F17 — a lightweight "who are you right now" before proof is collected or a task is closed | Without it the bet is false at launch and every proof record on a shared phone is mis-attributed |
| 2 | **Add a working "tell" path to the ship gates** (recurring + one recommended standing rule + templates reaching real staff), co-equal with the moat | Today the ship gate is the differentiator (F1) while the actual pain-killer — the manager's mornings spent assigning work — is soft-gated. That is backwards for the user |
| 3 | **Extend "no scorecard" to managers and properties**; reframe F22/F25 as exceptions-to-help, not rankings; give the manager her own record as her defense | The anti-surveillance machinery all points down at staff while F18/F22/F25 build exactly the manager's stated fear. She is a named top-3 deal-blocker |
| 4 | **Add a staff-facing bet surface** — a first-run and persistent framing that says "this is your record, no fines" | The bet currently exists only as internal decisions. Staff meet another form the boss makes them fill. We would be testing completion rate against a mechanism never shown to the person it must convince |
| 5 | **Cold-start: RentOk pre-provisions a starter routine set per property type at onboarding** | Otherwise setup falls on the overworked manager *before* the tool returns any value — the week-2 abandonment path |
| 6 | **Ramu: keep his rounds as tasks; scope his continuous visitor register explicitly OUT** and soften the persona claim | His register is a different primitive. Right now he is a named persona the feature set routes around |
| 7 | **Pull basic custom cadences into cycle** (Mon/Wed/Fri, a monthly date) | Common-area cleaning and meter readings do not fit daily/weekly/monthly cleanly. Too common to defer |

---

## Behavior decisions to add as D19+ (define before an engineer guesses)

### Standing-rule integrity
- **Dematch** — when an entity stops matching (the room fills), the open instance must *suggest close*, mirroring F1. Distinct from the rule's cadence stop. Without this, "clean until filled" leaves stale tasks on occupied rooms — the self-cleaning promise inverts into clutter.
- **Assignee resolution** — a rule must resolve *which* staff member each spawned task lands on (role-based or fixed on the rule). Unassigned tasks reach nobody's runner.
- **Idempotency** — a rule must not spawn a second instance for a scope member that still has one open; roll forward or mark missed. Otherwise a 30-day vacancy produces 30 open "clean 101" tasks.
- **Blast-radius preview** — show how many entities/staff a rule will hit *before* activation.
- **Management surface** — list, edit, pause/resume, archive rules, and see what a rule has spawned. Today a rule can be switched on and never inspected or stopped.
- **Safety cap** — max active instances or max duration per rule, so a stop condition that never fires cannot run away.
- **Transitional exclusion** — rule matching must skip entities mid-transition (under notice, move-out in progress) so a rule does not collide with the event-created task.
- **Rule edit/delete semantics** — stops future spawns only; existing instances finish on their own status and keep their proof; narrowing scope cancels only not-yet-started instances.

### Notifications
- **F40 is missing the two most basic events** — *assigned to you* and *rejected / sent back*. Without them staff learn of a task only when a due reminder fires, and rework never starts.
- **Deeplink** — the WhatsApp message must open the specific task in the runner. That is the literal step between "notified" and "doing it".
- **Batching** — per-person daily digest rather than one message per task. At 200 rooms × a daily rule × fan-out, WhatsApp gets throttled or the number flagged, and D17 made it the sole channel with no fallback.
- **Send window** — a default quiet window (e.g. 8am–9pm) even without configurable quiet hours, so escalation does not wake an owner at 2am.

### Lifecycle
- **Assignee quits mid-task** (the core churn case) — open assigned tasks route to a reassignment queue with the manager notified; submitted proof keeps its historical actor reference; self-to-dos archive rather than vanish.
- **Manager deactivated** — transfer owned standing rules and pending reviews to a successor; rules must not keep firing under a deactivated owner. Contradicts "survives the manager's absence" otherwise.
- **Linked entity deleted / merged / changed** — define read-time resolution for every terminal state and a "no longer exists" state that neutralises the suggestion rather than erroring.
- **Payment states** — the suggestion fires **only on fully settled**. Partial, refunded, reversed, or waived must never suggest close. A task suggesting "done" because a due was *waived* is a wrong suggestion on a money record.
- **Template versioning** — pin each instance to the template version it was created from; validate (F36) against that pinned version. Otherwise editing a live template while a phone holds a stale offline copy loses work or corrupts the record.
- **Duplicate complaints** — a recurring check that fails daily must link to the existing open complaint, not raise a new one each cycle. Also define the default assignee for a check-raised complaint.

### Offline and time
- Overdue and escalation are **server-computed on sync**, never on the device clock; missed reminders collapse into a single "you have N overdue" rather than replaying a burst.
- Offline-created tasks get a client temp-ID and validate on reconnect; creating **rules or recurring schedules** offline is blocked (needs the server).

### Consistency and orphans
- **Type-1 tasks have no category** — F20 promises one category-filtered list across three sources, but only created tasks carry a category (F32). Statically map the existing system tasks onto the same category set (cheap; stays inside D6).
- **Cross-source ordering** — define one this-cycle sort key spanning sources (e.g. due date, then priority); do not let it wait on the deferred registry ranking.
- **F8's "history / navigation" has no view** — nothing renders "every task ever linked to room 204". Either build the entity-scoped history view or downgrade the claim.
- **F23 shift handover** depends on a shift/roster concept that is explicitly out of scope — redefine it as a manual bulk reassign of open tasks, or defer it.
- **Self-to-dos vs escalation and export** — F18 escalates "every task" to the owner while F7/D9 calls self-to-dos private. Carve self-to-dos out of escalation, and state whether they appear in the audit export.
- **Manager free-text tasks bypass F13** — a one-off task typed in English reaches a Hindi-only cleaner untranslated.
- **Pooled task, simultaneous offline completion** — first synced completion wins and closes it; later ones attach as corroborating.
- **Reassignment and partial proof** — never carry one person's un-submitted proof under another person's name.
- **No-app assignees** (a guard with no smartphone) — either block assignment or provide a manager-proxy completion path; exclude them from auto-escalation.

---

## What the reviewers judged sound

The "prove" half is genuinely well built. The deferral of the [D]-tier and [I]-tier Audit capabilities is a real park, not a drop. Two flows trace clean end to end: the self-to-do flow, and the approve → close → where-it-shows tail. The v2 backlog is honest.

## Verdict

**"Prove" is strong; "tell" is capable but adoption-fragile; "see" is thin and the wrong shape** (rows and a grid where the founder needs an exception stream). The single highest-leverage thread is shared-device reality — resolving it unblocks calls 1 and 4 and most of the identity findings.
