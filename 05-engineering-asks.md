---
title: "Task Module: what we need from engineering"
date: 2026-09-05
first_written: 2026-08-04
owner: "Sanchay"
for: "Nimit, Jatin, Vivek"
status: "waiting on engineering"
changelog: "2026-09-05: reconciled with the 5 Aug decisions (M5 and M6 added to the stage lists; three questions that already had answers moved to the settled list; F29's note corrected); every item in the pricing table now carries its name and its spec section; defects section added for Nimit. 2026-08-04: first version."
tags: [rentok, tasks, handoff]
---

# What we need from engineering

## What is in here

The product side is done. **We cannot draw a V1 line without your cost.** That is the ask. The numbers, so they line up with the tables below: 56 requirements in the bands, numbered F1 to F59 (six more are deferred). The seven stages hold 43 of them plus the prerequisites and migrations, 48 rows below. The other 13 are ruled in but not yet placed in a stage and are not yours to price. This file holds the three asks, every item you are pricing with its name and where its spec lives, the code facts we verified so you do not have to, what is settled, what is still yours to decide, and four live defects. It is for Nimit, Jatin and Vivek. It is not the spec: acceptance, data shapes and edges are in [04-spec-stages-1-2.md](04-spec-stages-1-2.md).

## Contents

- [1. Price the seven stages](#1-price-the-seven-stages)
- [2. Find and authenticate what calls the trigger](#2-find-and-authenticate-what-calls-the-trigger)
- [3. The permission proxy: what is settled, what we still ask](#3-the-permission-proxy-what-is-settled-what-we-still-ask)
- [4. Code facts we verified](#4-code-facts-we-verified)
- [5. Settled since the first handoff](#5-settled-since-the-first-handoff)
- [6. Still yours to decide](#6-still-yours-to-decide)
- [7. Two decisions not up for reopening](#7-two-decisions-not-up-for-reopening)
- [8. Four live defects, for Nimit](#8-four-live-defects-for-nimit)

---

## 1. Price the seven stages

Rough is fine: days or weeks per stage, not a Gantt. We will draw the line where your number lands. Price the backend and the screens as two numbers on the same rows, so the line can be drawn on either. Stage 2 has no designs yet: price the build assuming designs arrive; do not price design time or waiting. Estimates go on the stage's issue in Linear, project [Task Module redesign](https://linear.app/rentok/project/task-module-redesign-4bafc030f838); the tables below link each row.

| Stage | What it does | Items | Where | Linear |
|---|---|---|---|---|
| **1** | Make what already exists safe (invisible to users) | 10 | [spec, stage 1](04-spec-stages-1-2.md#3-stage-1--make-what-already-exists-safe) | [REN-661](https://linear.app/rentok/issue/REN-661) |
| **2** | Give the work an owner, a real cadence, and something worth filling in | 12 | [spec, stage 2](04-spec-stages-1-2.md#4-stage-2--give-the-work-an-owner-a-real-cadence-and-something-worth-filling-in) | [REN-662](https://linear.app/rentok/issue/REN-662) |
| **3** | Make it chase itself | 7 | [build sequence, stage 3](03-build-sequence.md#stage-3--make-it-chase-itself) | [REN-663](https://linear.app/rentok/issue/REN-663) |
| **4** | The proof | 4 | [build sequence, stage 4](03-build-sequence.md#stage-4--the-proof) | [REN-664](https://linear.app/rentok/issue/REN-664) |
| **5** | The fault loop: a failed check raises a complaint | 3 | [build sequence, stage 5](03-build-sequence.md#stage-5--the-fault-loop) | [REN-665](https://linear.app/rentok/issue/REN-665) |
| **6** | Letting each level see: review, one list, history, insight | 6 | [build sequence, stage 6](03-build-sequence.md#stage-6--letting-each-level-see) | [REN-666](https://linear.app/rentok/issue/REN-666) |
| **7** | The on-ramp and the things that run themselves | 6 | [build sequence, stage 7](03-build-sequence.md#stage-7--the-on-ramp-and-the-things-that-run-themselves) | [REN-667](https://linear.app/rentok/issue/REN-667) |

**Stage 2 is the biggest and splits** if we need a smaller first ship: **2a** = F10, F16, M1 with P0, F5, F4, F32 · **2b** = M6, F29, F30, F9, F13.

### Stages 1 and 2, item by item

Every item has a full section in the spec: what it is, what the code does today, numbered acceptance, data and API, edges. The order inside each stage is the spec's build order.

| Stage | Item | What it is | Spec | Linear |
|---|---|---|---|---|
| 1 | P1 | A reliable, authenticated scheduler that fires recurring work | [P1](04-spec-stages-1-2.md#p1--a-reliable-authenticated-scheduler) | [REN-669](https://linear.app/rentok/issue/REN-669) |
| 1 | M5 | Rename the three question types live data uses but the code does not know (in 48.2% of checklists) | [M5](04-spec-stages-1-2.md#m5--rename-the-live-question-types) | [REN-670](https://linear.app/rentok/issue/REN-670) |
| 1 | F44 | A checklist with open tasks cannot be edited; save it as a new one instead | [F44](04-spec-stages-1-2.md#f44--a-checklist-with-open-tasks-cannot-be-edited) | [REN-671](https://linear.app/rentok/issue/REN-671) |
| 1 | F36 | Every submission is validated on the server against its checklist | [F36](04-spec-stages-1-2.md#f36--every-submission-is-validated-on-the-server) | [REN-672](https://linear.app/rentok/issue/REN-672) |
| 1 | F26 + M2 | Access control: five permissions; managers keep today's access, staff default to seeing only their own | [F26 + M2](04-spec-stages-1-2.md#f26--m2--access-control) | [REN-673](https://linear.app/rentok/issue/REN-673) |
| 1 | F41 | The runner proves who is submitting; links expire | [F41](04-spec-stages-1-2.md#f41--the-runner-proves-who-is-submitting-links-expire) | [REN-674](https://linear.app/rentok/issue/REN-674) |
| 1 | F37 | An edit log on every task, and a lock after submission | [F37](04-spec-stages-1-2.md#f37--an-edit-log-and-a-lock-after-submission) | [REN-675](https://linear.app/rentok/issue/REN-675) |
| 1 | F38 | Archive and restore instead of deleting | [F38](04-spec-stages-1-2.md#f38--archive-and-restore-instead-of-deleting) | [REN-676](https://linear.app/rentok/issue/REN-676) |
| 1 | F46 | Photo questions open the camera, never the gallery | [F46](04-spec-stages-1-2.md#f46--photo-questions-open-the-camera-never-the-gallery) | [REN-677](https://linear.app/rentok/issue/REN-677) |
| 1 | F45 | The server decides what is late; several missed reminders arrive as one message | [F45](04-spec-stages-1-2.md#f45--the-server-decides-what-is-late-missed-reminders-arrive-as-one-message) | [REN-678](https://linear.app/rentok/issue/REN-678) |
| 2 | F10 | Scope a task to the property, a floor, specific rooms, or areas | [F10](04-spec-stages-1-2.md#f10--scope-a-task-to-the-property-a-floor-specific-rooms-or-areas) | [REN-679](https://linear.app/rentok/issue/REN-679) |
| 2 | F16 | Assign to several people two ways: pooled or one-each | [F16](04-spec-stages-1-2.md#f16--assign-to-several-people-two-ways-pooled-or-one-each) | [REN-680](https://linear.app/rentok/issue/REN-680) |
| 2 | M1 | Room cleaning becomes an ordinary recurring task, pooled per room | [M1](04-spec-stages-1-2.md#m1--room-cleaning-becomes-an-ordinary-recurring-task-pooled-per-room) | [REN-681](https://linear.app/rentok/issue/REN-681) |
| 2 | P0 | The scheduler skips any routine with nobody assigned | [P0](04-spec-stages-1-2.md#p0--the-scheduler-skips-any-routine-with-nobody-assigned) | [REN-682](https://linear.app/rentok/issue/REN-682) |
| 2 | M6 | The checklist structure gains sections and branching | [M6](04-spec-stages-1-2.md#m6--structure-gains-sections-and-branching) | [REN-683](https://linear.app/rentok/issue/REN-683) |
| 2 | F29 | The question types a real inspection needs | [F29](04-spec-stages-1-2.md#f29--the-question-types-a-real-inspection-needs) | [REN-684](https://linear.app/rentok/issue/REN-684) |
| 2 | F30 | Per-item settings: required, photo required, reference picture, note, sections | [F30](04-spec-stages-1-2.md#f30--per-item-settings) | [REN-685](https://linear.app/rentok/issue/REN-685) |
| 2 | F5 | Create a recurring task on a real cadence | [F5](04-spec-stages-1-2.md#f5--create-a-recurring-task-on-a-real-cadence) | [REN-686](https://linear.app/rentok/issue/REN-686) |
| 2 | F4 | Create a one-off task at any time: assign it, set a due date and time | [F4](04-spec-stages-1-2.md#f4--create-a-one-off-task-at-any-time) | [REN-687](https://linear.app/rentok/issue/REN-687) |
| 2 | F32 | A task carries a category, a priority, a description, and an optional end date | [F32](04-spec-stages-1-2.md#f32--a-task-carries-a-category-a-priority-a-description-and-an-optional-end-date) | [REN-688](https://linear.app/rentok/issue/REN-688) |
| 2 | F9 | A checklist library to start from, which the operator can change | [F9](04-spec-stages-1-2.md#f9--a-checklist-library-to-start-from) | [REN-689](https://linear.app/rentok/issue/REN-689) |
| 2 | F13 | Starter templates ship in Hindi as well as English | [F13](04-spec-stages-1-2.md#f13--starter-templates-ship-in-hindi-as-well-as-english) | [REN-690](https://linear.app/rentok/issue/REN-690) |

### Stages 3 to 7, item by item

No spec exists for these yet, on purpose: stage 2 meeting real managers will rewrite it. Each item's one-line requirement and its "without it" line are in [02-requirements.md](02-requirements.md); the stage's ships-list and what depends on what are in [03-build-sequence.md](03-build-sequence.md).

| Stage | Item | What it is |
|---|---|---|
| 3 | F18 | Due dates, overdue, reminders and escalation |
| 3 | F40 | Notifications: four moments, all batched |
| 3 | F24c | Skip or reschedule a single occurrence |
| 3 | F33a | Reassign a task, including by the person holding it |
| 3 | F15a | A "couldn't do it" outcome with a reason |
| 3 | F53 | A manager can complete a task on behalf of someone without a smartphone |
| 3 | F51 | When someone leaves, their open work returns to the manager to reassign or close |
| 4 | F11 | Do the task in the runner, with proof |
| 4 | F39 | Photos are compressed on the phone before upload, and the local copy is deleted after |
| 4 | F12 | Partial work survives a tab close, a network drop, and a phone restart |
| 4 | F17 | Every staff member sees their own tasks and their own record |
| 5 | F31 | A single item can carry a problem, and a problem does not fail the task |
| 5 | F2 | A reported problem hands over a complaint, pre-filled, raised with one tap |
| 5 | F54 | When raising a complaint from a failure, show the open ones for that room first |
| 6 | F19 | Review: approve, reject with a reason, or send back for rework |
| 6 | F20 | One list for the manager: all three sources, filtered by category, sorted by due date then priority |
| 6 | F57 + M3 | System-raised tasks carry the same categories as everything else (M3 maps the live alerts onto them) |
| 6 | F8 | Link a task to a real thing, and see everything ever done to it |
| 6 | F21 | The first insight cut: completion, on-time rate, problems by room, week over week |
| 6 | F22 | The exception view: what needs attention; the manager sees her own |
| 7 | F48 | Manage repeating tasks: list, edit, pause, resume, archive, see what each has produced |
| 7 | F49 | Show how many rooms or people a routine will affect before it is switched on |
| 7 | F50 | A routine that only produces ignored work pauses itself |
| 7 | F7 | Keep tasks for yourself, with reminders |
| 7 | F3 | A finished move-out creates the room-prep task |
| 7 | F58 | Turn an alert into work: one task per item |

**Also:** tell us anything in the dependency map ([03-build-sequence.md](03-build-sequence.md#the-dependency-map)) that is wrong. It is our reading of the code, not yours.

---

## 2. Find and authenticate what calls the trigger

This is P1, the first item of stage 1. A scheduler fires today (the product works), but **nothing in the repo registers it**, and the trigger route has no authentication. Every recurring task in production depends on a caller nobody has identified. Backend issue **#6363**.

We would chase this regardless of where the cut falls. If nothing reliable calls it, recurring work can stop and nobody finds out until a manager asks why the cleaning list is empty. The job is to find the caller and authenticate the endpoint, not to build a scheduler. If the caller turns out to be unowned or unreliable, replacing it with a registered job is a scope change you flag, not one you absorb.

---

## 3. The permission proxy: what is settled, what we still ask

**Settled (Sanchay, 4 Aug 2026):** the access migration defaults anyone without the three team flags (`view_team`, `add_team`, `edit_team`) to "see only my own". Everyone else keeps today's access. We picked those flags because there is no task-related flag anywhere in the team-member permission table (all 94 columns checked); managing the team is the closest existing signal for "hands out work". That is D69, correcting D13.

**Still asked of you** (Linear [REN-668](https://linear.app/rentok/issue/REN-668)): does that proxy hold against how properties actually set permissions today? If many properties give staff a team flag for some unrelated reason, the default lands wrong. You can see the data; we cannot.

---

## 4. Code facts we verified

So you do not have to. All read on 5 Aug 2026 against `rentok-backend` at `master`.

| Claim | Where | Consequence |
|---|---|---|
| Fan-out is gated on `schedule_team_members.length > 0 && system_purpose !== 'room_cleaning'`; **otherwise it creates one instance with `team_member_id: undefined`** | `taskScheduler.ts` | **P0 is a real code change, not a config one.** An unassigned routine fires tasks at nobody today. It must land with M1, because room cleaning deliberately relies on the unassigned path. |
| **Zero** task-related permission columns | `teamMemberProperty.ts` (94 columns) | D69 (staff default to see only my own) needs a proxy; see the permission-proxy question in section 3. |
| **Zero** `checkAuthInDb` calls | `taskController.ts`, `taskRoutes.ts` | "Keep today's access" would mean granting everything to everyone. Hence the staff default. |
| `submitTask` stores the doer's identity and location **from the request body**, unvalidated; token never expires | `taskController.ts` | F41 (the runner proves who is submitting) and F36 (server-side validation) are stage 1 for this reason. |
| Move-out complaint lookup filters on `tenant_id` + `tenant_checklist_item_id` with **no status filter**; the tenant-marked path does **no lookup at all** | `moveOutChecklistService.ts:614` | **F54's "show me the open ones" is new work, not a pattern to copy.** The path itself is a real seam for F3 (a finished move-out creates the room-prep task). |

---

## 5. Settled since the first handoff

The 4 Aug version of this file asked four questions it could not settle from the code (F44, F29, F58, M3). Three were answered the same week and never struck out; M3 stays open in section 6. It also asked for the permission proxy to be confirmed; the flags are confirmed, the practice question stays in section 3. Recorded here so nobody prices a question that has an answer.

| Was asked | Answer | Where it lives |
|---|---|---|
| F44: block editing a checklist with open tasks, or version-pin every task? | The **product behaviour** is settled: a checklist with open tasks cannot be edited; the operator saves it as a new copy. Whether you implement that as a refusal or as version-pinning underneath is yours (the first item in section 6, still yours to decide). | [F44 in the spec](04-spec-stages-1-2.md#f44--a-checklist-with-open-tasks-cannot-be-edited) |
| F58: can the existing pending-tasks feed carry an assign action? | No. The feed has no assign action today; F58 is a new build. | Sanchay, 4 Aug; the feed is documented in [reference/pending-tasks/](reference/pending-tasks/README.md) |
| D69's proxy flags | Confirmed: the three team flags. The practice question stays open (section 3). | [F26 + M2 in the spec](04-spec-stages-1-2.md#f26--m2--access-control) |
| F29: are pass/fail/NA and a 1 to 5 rating just configurations of the existing select? | Half right. Pass/fail/NA is a `select` with three options. **A rating is its own type carrying a scale**, not five options, because insights needs to average it. The committed type list is in [the spec, section 2](04-spec-stages-1-2.md#2-the-question-types-d84). | D84, 5 Aug |

---

## 6. Still yours to decide

Handed over with the spec. None of these is product's; each changes cost, not behaviour.

1. **F44:** refuse the edit with a 409, or version-pin each task to a checklist snapshot. Both give the settled behaviour; pinning solves more and costs more.
2. **F29:** how much runner work do the new question types need? We assume only several-photos, the instruction block, branching and sections are genuinely new.
3. **F4:** `schedule_id` and `template_id` on `task_instance` become nullable. How much reads those columns assuming they are set?
4. **F41:** the grace window for a submission in flight when its link expires. A number, not a principle.
5. **M6:** who else reads `task_template.structure` outside this repo (manager web, exports, reports)?
6. **M3:** mapping the live system alerts onto the shared categories. We assumed static and cheap.
7. **F37:** audit log volume. The spec proposes logging scheduler creation at the run level. Is that enough for a dispute?
8. **Defect 3 below, the cross-account template edit:** does it need fixing now, ahead of stage 1? It is live, needs no design, and F9's content lands on top of it.

---

## 7. Two decisions not up for reopening

Not because they are sacred, but because they were argued to a conclusion and re-deriving them costs more than they are worth. Both are in [CHANGELOG.md](CHANGELOG.md) with the rejected alternative.

- **No standing rules and no stored room-occupancy flag** (D64). Vacant-room readiness is F3 instead: a finished move-out creates the prep task, using the seam in section 4.
- **No fines and no per-person league table**, for staff or managers (D15, D22 as corrected by D71, D70). Per-person numbers come from one-each and single-assignee work only, never from pooled.

If either one blocks something in the build, say so and we will reopen it deliberately. That is different from it drifting back in.

---

## 8. Four live defects, for Nimit

Found while grounding the spec on 5 Aug 2026. Not requirements: bugs in shipped code. Full detail with file and line in [the spec's defects section](04-spec-stages-1-2.md#6-defects-found-while-grounding).

1. A team member can be sent someone else's task link, and anyone following it files work as that person.
2. A member assigned to many rooms is sent one room's link; the other rooms are unreachable from the message.
3. **Any account can edit any other account's checklist templates, and every account can edit RentOk's shared starter templates.** The guard reads the account id from the request body. This is the most serious and it is live now.
4. The task tables mix timestamps with and without time zone, so any comparison between them is out by five and a half hours. Must be fixed before anything computes lateness.
