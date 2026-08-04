---
title: "Task Module — Handoff to Engineering"
date: 2026-08-04
owner: "Sanchay"
for: "Nimit, Jatin"
status: "waiting on engineering"
tags: [rentok, tasks, handoff]
---

# Task Module — what we need from engineering

The product side is done. 43 requirements, ordered into seven stages that each ship on their own.
**We cannot draw a V1 line without your cost.** That is the ask.

Read [build-sequence.md](build-sequence.md) for the stages and what depends on what.
[feature-requirements.md](feature-requirements.md) has the requirements themselves.
Everything else is background.

---

## Three things we need

### 1. Price the seven stages

Rough is fine — days or weeks per stage, not a Gantt. We will draw the line where your number lands.

| Stage | What it is | Contents |
|---|---|---|
| **1** | Make what already exists safe. Invisible to users. | P1 · F26+M2 · F41 · F36 · F44 · F37 · F38 · F46 · F45 |
| **2** | Owner, cadence, and a library to start from | F10 · F16 · F5 · F4 · F32 · M1+P0 · F29 · F30 · F9 · F13 |
| **3** | It chases itself, and the record stays honest | F18 · F40 · F24c · F33a · F15a · F53 · F51 |
| **4** | Proof capture | F11 · F39 · F12 · F17 |
| **5** | Failed check raises a complaint | F31 · F2 · F54 |
| **6** | Review, one list, history, insight, owner view | F19 · F20 · F57+M3 · F8 · F21 · F22 |
| **7** | Routine management, my tasks, event-created work, alert→work | F48 · F49 · F50 · F7 · F3 · F58 |

**Stage 2 is the biggest and splits** if we need a smaller first ship: **2a** = F10, F16, F5, F4, F32,
M1+P0 · **2b** = F29, F30, F9, F13.

### 2. Confirm P1 — what actually calls `POST /tasks/trigger`?

There is **no cron registration anywhere in the repo**, and the route has no authentication. Every
recurring task in production depends on a caller nobody has identified. Backend issue **#6363**.

This is the one we would chase regardless of the cut. If nothing reliable calls it, recurring work can
stop and nobody finds out until a manager asks why the cleaning list is empty.

### 3. Confirm the permission proxy in D69

The access migration defaults **anyone without `view_team` / `add_team` / `edit_team`** to "see only my
own". We picked that because **there is no task-related flag anywhere in `team_member_property`** — we
checked all 94 columns. It is a stand-in for "can hand out work". Does it hold against how properties
actually set permissions today?

**Also:** tell us anything in the dependency map that is wrong. It is our reading of the code, not yours.

---

## Code facts we verified, so you do not have to

| Claim | Where | Consequence |
|---|---|---|
| Fan-out is gated on `schedule_team_members.length > 0 && system_purpose !== 'room_cleaning'`; **otherwise it creates one instance with `team_member_id: undefined`** | `taskScheduler.ts` | **P0 is a real code change, not a config one.** An unassigned routine fires tasks at nobody today. It must land with M1, because room cleaning deliberately relies on the unassigned path. |
| **Zero** task-related permission columns | `teamMemberProperty.ts` (94 columns) | D69 needs a proxy — see ask 3. |
| **Zero** `checkAuthInDb` calls | `taskController.ts`, `taskRoutes.ts` | "Keep today's access" would mean granting everything to everyone. Hence the staff default. |
| `submitTask` stores the doer's identity and location **from the request body**, unvalidated; token never expires | `taskController.ts` | F41 and F36 are stage 1 for this reason. |
| Move-out complaint lookup filters on `tenant_id` + `tenant_checklist_item_id` with **no status filter**; the tenant-marked path does **no lookup at all** | `moveOutChecklistService.ts:614` | **F54's "show me the open ones" is new work, not a pattern to copy.** The path itself is a real seam for F3. |

---

## Four things we could not settle from the code

1. **F44** — we specced "block editing a checklist that has open tasks" as cheaper than version-pinning
   every task. Both solve it. **Your call which is actually cheaper.**
2. **F29** — how much runner work do new question types need? We assumed pass/fail/NA and a 1–5 rating
   are configurations of the existing `select`, and that only multi-photo and the instruction block are
   genuinely new. Check that.
3. **F58** — can the existing pending-task feed carry an assign action, or does that surface need
   rebuilding? This decides whether F58 is small or large, and it is the most demo-able thing in the set.
4. **M3** — mapping the live system alerts onto the shared categories. We assumed static and cheap.

---

## Two things that are decided and not up for re-opening

Not because they are sacred, but because they were argued to a conclusion and re-deriving them costs
more than they are worth. Both are in [CHANGELOG.md](CHANGELOG.md) with the rejected alternative.

- **No standing rules and no stored room-occupancy flag** (D64). Vacant-room readiness is F3 instead —
  a finished move-out creates the prep task, using the seam above.
- **No fines and no per-person league table**, for staff or managers (D15, D22, D70). Per-person numbers
  come from fan-out and single-assignee work only, never from pooled.

If either one blocks something in the build, say so and we will reopen it deliberately — that is
different from it drifting back in.
