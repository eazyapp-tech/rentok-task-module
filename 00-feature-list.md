---
title: "Task Module: every feature on one page"
date: 2026-09-05
owner: "Sanchay"
status: "current; generated from 02-requirements.md v2.4, regenerate when that file changes"
tags: [rentok, tasks, feature-list]
---

# Every feature on one page

## What is in here

One line per item, nothing else: everything the redesign builds, grouped by the stage it arrives in; then what is in no stage yet; then what is parked and what revives it; then what we chose not to build. Three minutes. It is for scanning. The reasons are in [00-feature-map.md](00-feature-map.md) (plain words, by moment) and [02-requirements.md](02-requirements.md) (each item with what the operator loses without it). The build order and what depends on what are in [03-build-sequence.md](03-build-sequence.md). The labels (F for feature, P for prerequisite, M for migration) are the ones every other doc uses.

## Contents

- [The seven stages](#the-seven-stages)
- [In no stage yet](#in-no-stage-yet)
- [Parked, and what revives each](#parked-and-what-revives-each)
- [Not building](#not-building)

## The seven stages


### Stage 1: Make what already exists safe (invisible to users)

| | |
|---|---|
| P1 | A reliable, authenticated scheduler that fires recurring work |
| M5 | Rename the question types that live data uses but the code does not know |
| F44 | A checklist with open tasks cannot be edited; you save it as a new one instead |
| F36 | Every submission is validated on the server against the checklist it belongs to |
| F26 | Access control across the module: managers keep today's access, staff see only their own by default |
| M2 | The access-control migration: managers keep today's access, staff see only their own by default |
| F41 | The runner proves who is submitting; links expire |
| F37 | An edit log on every task, and a lock after submission |
| F38 | Archive and restore instead of deleting |
| F46 | Photo questions open the camera, never the gallery |
| F45 | The server decides what is late, and several missed reminders arrive as one message |

### Stage 2: Give the work an owner, a real cadence, and something worth filling in

| | |
|---|---|
| F10 | Scope a task to the property, a floor, specific rooms, or areas |
| F16 | Assign to several people two ways: pooled or one-each |
| M1 | Room cleaning becomes an ordinary recurring task, pooled per room |
| P0 | The scheduler must skip any routine with nobody assigned |
| M6 | The checklist structure gains sections, and items that appear only after an earlier answer |
| F29 | The question types a real inspection needs |
| F30 | Per-item settings |
| F5 | Create a recurring task on a real cadence |
| F4 | Create a one-off task at any time: assign it, set a due date and time |
| F32 | A task carries a category, a priority, a description, and (if recurring) an optional end date |
| F9 | A checklist library to start from, which the operator can change |
| F13 | RentOk's starter templates ship in Hindi as well as English; the operator writes in her own script |

### Stage 3: Make it chase itself

| | |
|---|---|
| F18 | Due dates, overdue, reminders and escalation |
| F40 | Notifications: four moments, all batched |
| F24c | Skip or reschedule a single occurrence |
| F33a | Reassign a task, including by the person holding it |
| F15a | A "couldn't do it" outcome with a reason |
| F53 | A manager can complete a task on behalf of someone without a smartphone |
| F51 | When someone leaves, their open work returns to the manager to reassign or close |

### Stage 4: The proof

| | |
|---|---|
| F11 | Do the task in the runner, with proof |
| F39 | Photos are compressed on the phone before upload, and the local copy is deleted after it |
| F12 | Partial work survives a tab close, a network drop, and a phone restart |
| F17 | Every staff member sees their own tasks and their own record |

### Stage 5: The fault loop

| | |
|---|---|
| F31 | A single item can carry a problem, and a problem does not fail the task |
| F2 | A reported problem hands over a complaint, pre-filled, raised with one tap |
| F54 | When raising a complaint from a failure, show the open ones for that room first |

### Stage 6: Letting each level see

| | |
|---|---|
| F19 | Review: approve, reject with a reason, or send back for rework |
| F20 | One list for the manager: all three sources, filtered by category, sorted by due date then priority |
| F57 | System-raised tasks carry the same categories as everything else |
| M3 | System-raised tasks are mapped onto the shared categories |
| F8 | Link a task to a real thing, and see everything ever done to it |
| F21 | The first insight cut: completion, on-time rate, problems by room, week-over-week |
| F22 | The exception view: what needs attention, and the manager sees her own |

### Stage 7: The on-ramp and the things that run themselves

| | |
|---|---|
| F48 | Manage repeating tasks: list, edit, pause, resume, archive, and see what each has produced |
| F49 | Show how many rooms or people a routine will affect before it is switched on |
| F50 | A routine that only produces ignored work pauses itself |
| F7 | Keep tasks for yourself, with reminders |
| F3 | A finished move-out creates the room-prep task |
| F58 | Turn an alert into work: one task per item, with an owner |

## In no stage yet

Wanted, ruled in, and not yet placed in a stage. They arrive when the stages are priced and a line is drawn.

| | |
|---|---|
| F14 | A comment thread on a task, with person tagging |
| F15b | Voice notes |
| F24a | Quick-capture an ad-hoc task on the spot |
| F24b | Approve and reject from the phone |
| F33b | Assign one task across many rooms or people at once |
| F52 | When a manager is deactivated, her rules and pending reviews transfer |
| F47 | New properties start with their routines already set up; existing ones are offered them |
| F25a | A weekly digest to the owner over WhatsApp |
| F59 | A visit log: a task that records arriving and leaving |
| F1 | A task shows the live state of the thing it is linked to |
| F43 | A freeform tag on a task, for filtering |
| F25b | Export the task record for an audit or a dispute |
| F27 | Task and rule creation built as a callable action, not a form-only path |
| M4 | Released to everyone, enabled account by account |

## Parked, and what revives each

| Feature | Revived when |
|---|---|
| F3b: the business creates tasks from its other events, beyond move-out | Cross-module hooks exist. The move-out half is already in as F3 |
| F6: a repeating task that watches a condition and creates work on its own | We have watched an operator want one |
| F42: lobby, lift and stairs as pickable places with their own history | After V1; tags carry it until then |
| F28: build tasks and rules by talking to the assistant | The RentOk AI work lands; F27 keeps the door open |
| F34: score a checklist from its ratings | V2 |
| F35: items that depend on a condition, and scanning an asset's code | V2 |
| Push notifications, email, a dedicated inbox | V2; WhatsApp first by decision |
| The full 65-entry list of system-raised tasks | Backend issue #6249 |
| Drafts kept on the server, beyond the phone | After V1 |
| Meter reading as a route: many stops, each with a photo, monthly | V2; needs a task target that is not a room or a property |
| Approval chains, delegation, quiet hours, work orders, shared devices | V2, each on its own evidence |

## Not building

| Not building | Why, in one line |
|---|---|
| Fines, salary deductions, or a staff scorecard, for anyone | Staff who believe the tool can cost them money stop filling it honestly |
| Anything that acts without a person confirming | A wrong automatic action on money or a complaint is worse than a missed manual one |
| A free-form if-this-then-that rule builder | A developer tool, not an operator tool |
| High-frequency logs, such as a motor's on and off times | That is telemetry, not work |
| Tracking licence and certificate expiry dates | A renewal is a repeating task with a date the manager sets |
| Attendance and shift clocking | A different product |
| A native Task tab in the mobile app | The task pages reach the app through the existing web view |
| Rebuilding move-in and move-out | Move-out already records deposit deductions correctly; reuse it |
| The guard's visitor register | His paper register is his job and his dignity |
