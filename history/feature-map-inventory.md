---
title: "Feature map: the inventory behind 00-feature-map.md"
date: 2026-09-05
owner: "Sanchay"
status: "ruled 2026-09-05 (go on all seven calls); the map was written once from this"
tags: [rentok, tasks, feature-map, inventory]
---

# Feature map inventory

## What is in here

The audit trail behind [00-feature-map.md](../00-feature-map.md): every item the map must place, where it came from, the bucket proposed for it and the one-line reason, the moment of the property's life it belongs to, and the calls put to Sanchay before the map was written. Nothing here is new product. Every item is a locked requirement (F, P or M number from [02-requirements.md](../02-requirements.md)) or a locked decision (D number from [CHANGELOG.md](../CHANGELOG.md)). If the map and this file disagree, the map was written after a ruling that this file records at the bottom.

Buckets, as the feature-map method defines them: **spine** (remove it and one of the twelve locked sentences becomes false), **supporting** (strengthens the spine; ships without changing the thesis; the reason names what it protects), **parked** (good, wrong dependencies; the condition that revives it is named), **cut** (not building, with the human reason).

## Contents

- [1. Sources walked](#1-sources-walked)
- [2. The moments](#2-the-moments)
- [3. Spine](#3-spine)
- [4. Supporting](#4-supporting)
- [5. Parked, and what revives each](#5-parked-and-what-revives-each)
- [6. Cut, and why](#6-cut-and-why)
- [7. The foundation nobody sees](#7-the-foundation-nobody-sees)
- [8. The two sections other readers need](#8-the-two-sections-other-readers-need)
- [9. Vocabulary evidence](#9-vocabulary-evidence)
- [10. Calls for Sanchay](#10-calls-for-sanchay)
- [11. Rulings](#11-rulings)

## 1. Sources walked

| Source | What was taken |
|---|---|
| 02-requirements.md, v2.4 | All 56 F rows with their "without it" lines; P0, P1; M1 to M6; the Later list; the Not building list |
| 03-build-sequence.md | "What is in no stage"; the three arguable sequencing calls; F27 as a build rule |
| 01-brief.md | The problem, the chain of accountability (four people), the mission (tell, prove, see), the bet, "What we will not build this cycle", "What success looks like" |
| CHANGELOG.md | The twelve locked sentences; D1, D15, D22, D64, D65, D69, D71, D76, D80, D81, D82, D83, D84, D85 as the rulings the map leans on |
| Backend issues #6249, #6363 | The parked registry; the scheduler's caller |

Count: 56 F items + 2 P + 6 M + 11 parked lines + 9 cut lines = 84 things to place. Every one appears below exactly once.

## 2. The moments

The map is organised as the property's life, not as modules. Seven moments, in the order a property meets them, then one cross-cutting section for the foundation nobody sees.

| # | Moment | Who is in it | What happens |
|---|---|---|---|
| 1 | Setting the property up | Priya, the owner | Picking checklists from a library in her own language, deciding what repeats and where, who does it and how they share it |
| 2 | The morning | Staff, Priya | The day's work arrives on its own; each person sees their own list; the manager sees one list for everything |
| 3 | Doing the work | Staff, Ramu | The runner on a cheap phone in a basement: proof, camera only, work that survives a dropped signal, saying "couldn't do it", passing work on at the end of a shift, work recorded for someone without a phone |
| 4 | When something is wrong | Staff, Priya | An item carries a problem without failing the person; one tap raises the complaint, pre-filled; the open ones for that room show first; lateness and escalation |
| 5 | The manager's day | Priya | A one-off task on the spot, review, skipping one occurrence, turning an alert into work, keeping her own to-dos, managing the routines |
| 6 | The owner's week | The owner, Priya | The exception view for each, the first insights, the history of a room or a tenant |
| 7 | When people change | Priya, the owner | A staff member leaves, a manager is deactivated, a new property starts |
| 8 | The foundation nobody sees | Engineering | What makes the record worth having: permissions, validation, the edit log, expiry, archive, the migrations |

Cross-cutting mechanics named once, after the moments: nothing acts on its own; the proof belongs to the person who collected it; a task shows a linked thing's live state and never writes into it; three sources, one list. The map opens and closes on the thesis (sentence 8 and D82).

## 3. Spine

Remove it and one of the twelve sentences becomes false. The sentence it holds up is named.

| Item | What it is | Holds up | Moment | Why it is spine |
|---|---|---|---|---|
| F9 | A checklist library to start from | sentence 6 (tell) | 1 | Setup is a blank box otherwise, which is where a busy manager stops |
| F13 | Starter templates in Hindi as well as English | sentence 6 | 1 | A Hindi-first manager rewrites every template before staff can use it |
| F5 | A recurring task on a real cadence | sentence 6, 8 | 1 | Alternate-day cleaning and the 1st-of-month reading cannot be said otherwise |
| F10 | Scope a task to the property, a floor, rooms, or areas | sentence 6 | 1 | Without it every task covers the whole property and per-room accountability cannot exist |
| F16 | Assign to several people: pooled or one-each | sentence 10 | 1 | "Someone clean the lobby" and "each guard does his round" become the same thing |
| F32 | Category, priority, description, optional end date | sentence 5 | 1 | The one list has nothing to filter or sort by |
| F29 | The question types a real inspection needs | sentence 6, 7 | 3 | An inspection cannot record "not applicable", a score, or a meter reading |
| F30 | Per-item settings (required, photo required, reference picture, note, sections) | sentence 7 | 3 | "Required" means nothing, so the one photo a dispute needs is the one skipped |
| F18 | Due dates, overdue, reminders, escalation | sentence 6, 8 | 4 | Nothing is ever late and a missed day silently disappears |
| F40 | Notifications: four moments, all batched | sentence 6 | 2 | At 200 rooms an unbatched routine gets the WhatsApp number blocked and staff mute the only channel |
| F20 | One list for the manager, all three sources | sentence 5 | 2 | She checks three places, so she checks none |
| F57 | System-raised tasks carry the same categories | sentence 5 | 2 | The single filter breaks for one of its three sources on day one |
| F11 | Do the task in the runner, with proof | sentence 7 | 3 | No evidence the work happened, which is the whole product |
| F12 | Partial work survives a tab close, a network drop, a restart | sentence 7 | 3 | A cleaner half-way through loses everything when the signal drops, and does not start again |
| F46 | Photo questions open the camera, never the gallery | sentence 7 | 3 | One gallery upload makes every photo in the system worthless as evidence |
| F17 | Every staff member sees their own tasks and their own record | sentence 7 | 3 | Otherwise the proof belongs to the owner and the bet is not built |
| F19 | Review: approve, reject with a reason, send back | sentence 6 (see) | 5 | Submission and completion mean the same thing otherwise |
| F22 | The exception view; the manager sees her own | sentence 6 (see) | 6 | The owner is still asking managers how things are going, the position he pays us to leave |
| F31 | An item can carry a problem without failing the task | sentence 7 | 4 | Otherwise reporting a fault fails the reporter, so faults stop being reported |
| F2 | A reported problem raises a complaint, pre-filled, one tap | sentence 1, 4 | 4 | A fault found during an inspection dies in a form; the single thing S2L is waiting for |
| F4 | Create a one-off task at any time | sentence 5 | 5 | Nothing ad-hoc can be given to anyone; the manager is back on WhatsApp |
| F7 | Keep tasks for yourself, with reminders | sentence 5 | 5 | The third source; the work she owes herself stays on paper |
| F58 | Turn an alert into work: one task per item | sentence 9 | 5 | The alert shows a problem but can never become work with an owner |
| F3 | A finished move-out creates the room-prep task | sentence 8, 12 | 7 | The one event we know creates work; without it an empty room waits on the manager's memory |
| F48 | Manage repeating tasks: list, edit, pause, resume, archive, see what each produced | sentence 8 | 5 | A routine she cannot check or stop is not a system, it is a trap |

25 spine items.

## 4. Supporting

Strengthens the spine; ships without changing the thesis. The reason names what it protects.

| Item | What it is | Protects | Moment | Why it is supporting, not spine |
|---|---|---|---|---|
| F39 | Photos compressed on the phone; local copy deleted after | the 2G ship gate | 3 | A mechanism under F11, not a promise of its own |
| F21 | The first insight cut: completion, on-time rate, problems by room, week over week | seeing | 6 | The owner's exception view is the promise; the insight cut deepens it |
| F8 | Link a task to a real thing and see everything done to it | seeing, disputes | 6 | "What was done to room 204 this year" is the question a dispute asks; D68 ruled the link itself not ship-blocking |
| F49 | Show a routine's reach before switching it on | F5, F16 | 1 | Stops the 200-tasks-a-day flood; a guard on the spine, not the spine |
| F50 | A routine that only produces ignored work pauses itself | F48 | 5 | One forgotten routine otherwise buries the list |
| F53 | A manager completes a task on behalf of someone without a smartphone | F17, F21, F22 | 3 | Ramu's work otherwise reads as failure about the one person who never fails |
| F15a | A "couldn't do it" outcome with a reason | F17 | 3 | Blocked work otherwise looks identical to ignored work |
| F24c | Skip or reschedule a single occurrence | F5, F48 | 5 | A festival otherwise means switching the whole routine off and forgetting to switch it back on |
| F51 | When someone leaves, their open work returns to the manager | F21, F22 (the record) | 7 | In a business defined by churn, every resignation otherwise orphans work; Band B by D67, the record would lie |
| F33a | Reassign a task, including by the person holding it | F17 | 3 | The guard going off duty at 10pm otherwise wakes the manager or goes overdue against himself |
| F54 | When raising a complaint from a failure, show the open ones for that room first | F2 | 4 | One leaking tap otherwise becomes seven complaints in a week |
| F14 | A comment thread on a task, with tagging | F19 | 5 | Band C: when work fails there is nowhere to say why |
| F15b | Voice notes | F15a, F14 | 3 | Band C: staff who cannot type comfortably explain nothing |
| F24a | Quick-capture an ad-hoc task on the spot | F4 | 5 | Band C: the manager on her walk-round writes it on her hand |
| F24b | Approve and reject from the phone | F19 | 5 | Band C: review otherwise waits for a desk |
| F33b | Assign one task across many rooms or people at once | F10, F16 | 1 | Band C: a 200-room property otherwise means doing it 200 times |
| F52 | When a manager is deactivated, her rules and pending reviews transfer | F48, F19 | 7 | Band C: the sibling of F51; a manager leaving is rare where staff leaving is constant |
| F47 | New properties start with routines already set up; existing ones are offered them | F9 | 7 | Band C by D78: routines are created unassigned, so a new property with no staff gets no failures |
| F25a | A weekly digest to the owner over WhatsApp | F22 | 6 | Band C by D78: F22 is the promise; the digest is a reminder to look |
| F59 | A visit log: a task that records arriving and leaving | F11 | 3 | Band C: for operators whose supervisors travel between buildings (S2L asked) |
| F1 | A task shows the live state of the thing it is linked to | sentence 2 | 6 | Band C by D80: the sentence describes how linking behaves when it exists; F8 carries linking |
| F43 | A freeform tag on a task, for filtering | F20 | 6 | Band C: work that maps to no room or tenant can otherwise not be grouped |
| F25b | Export the task record for an audit or a dispute | F17, F8 | 6 | Band C: proving a year of compliance otherwise means screenshots |
| F27 | Task creation built as a callable action, not a form-only path | sentence 11 | build rule | A constraint on F4, F5 and F58 as they are built, not a feature; retrofitting means writing them twice |

24 supporting items; F45 sits in section 7, the foundation.

## 5. Parked, and what revives each

From the requirements' "Later" list. Each names the condition that revives it.

| Item | What it is | Revived when |
|---|---|---|
| F3b | The business creates tasks from its other events, beyond move-out | Cross-module hooks exist (V1.1). The move-out half is already in as F3 |
| F6 | A recurring task that watches a condition (standing rules) | An operator is watched wanting one. Three of the five routines it was meant to serve turned out to be events, not conditions (D64) |
| F42 | Operator-managed areas (lobby, lift, stairs) as pickable places with their own history | V1.1; tags carry it until then |
| F28 | Build tasks and rules by talking to the assistant | The RentOk AI work lands; F27 keeps the door open (sentence 11) |
| F34 | Score a checklist from its ratings | V2 |
| F35 | Conditional items, and scanning an asset's code | V2 |
| Push notifications, email, a dedicated inbox | Other channels | V2; WhatsApp-first by decision (D17), not omission |
| The full 65-entry system task registry | Every system-raised task the home screen could show | Backend issue #6249; the pack is in reference/pending-tasks/ |
| Server-side drafts | Drafts kept beyond the phone | V1.1; the on-phone save (D12) is in F12 |
| Meter reading as a route | Many stops, each with a photo, monthly, gating invoicing | V2; needs a task target that is not a room or a property |
| Advanced review, assignment, escalation, work orders, shared-device | Approval chains, delegation, quiet hours, request kinds | V2, each on its own evidence |

## 6. Cut, and why

From the requirements' "Not building" and the Brief's "What we will not build this cycle". Each has a human reason, not a cost reason.

| Not building | Why |
|---|---|
| Fines, salary deductions, or a staff scorecard, for anyone including managers | The bet. Staff who believe the tool can cost them money stop filling it honestly, and the proof collapses for everyone including the owner (D15, D22) |
| Anything that acts without a person confirming | Sentence 1. A wrong automatic action on a money record or a complaint is worse than a missed manual one (D1) |
| A free-form if-this-then-that rule builder | A developer tool, not an operator tool. Open-ended power comes through the assistant later (D7, then D64) |
| High-frequency logs, such as a motor's on and off times | That is telemetry, not work; a due date is the wrong shape for it (D81) |
| Tracking licence and certificate expiry dates | A fire NOC renewal is a recurring task with a date the manager sets; we do not watch expiries for her (D81) |
| Attendance and shift clocking | A different product; folding it in blurs what this module is for |
| A native Task tab in the mobile app | The runner and the manager's list reach the app through the existing web view (D11) |
| Rebuilding move-in and move-out | Move-out is already a task that records deposit deductions correctly; reuse it (D14, D61, sentence 12) |
| The guard's visitor register | His paper register is his job and his dignity; position the app as replacing it and he resists (the Brief's Ramu) |

## 7. The foundation nobody sees

Band A plus the prerequisites and migrations. In the map these are one section, written for a non-engineer: what makes the record worth having. They are spine by the test (sentence 7 is false if the record can be faked) but they are not moments, so they get their own place.

| Item | What it is, in plain words |
|---|---|
| F26 + M2 | Who may see, create, review and archive; staff see only their own by default |
| F41 | The runner knows who is submitting; a link stops working when its window closes |
| F36 | Every submission is checked against the checklist it belongs to before it is stored |
| F44 | A checklist in use cannot be changed under the people using it; you save a new copy |
| F37 | Every change is logged; a submitted record cannot be quietly altered |
| F38 | Nothing is deleted; things are archived and can be brought back |
| F45 | The server decides what is late; a phone with a wrong clock cannot create a false overdue |
| P1 | The thing that fires recurring work is found, owned and locked (backend #6363) |
| P0 | A routine with nobody assigned creates no work, instead of failures against nobody |
| M1 | Room cleaning becomes an ordinary task, so every cleaning records who cleaned |
| M5 | Three question types that live data already uses are given their real names |
| M6 | A checklist can hold sections and items that appear only after an earlier answer |
| M3 | System-raised tasks are mapped onto the shared categories |
| M4 | Released to everyone, switched on account by account |

## 8. The two sections other readers need

**How we talk about it** (for marketing, sales, support, and anyone outside product): the twelve locked sentences, quoted exactly, and a "never say" list: no fines, no scorecard, no surveillance, no "watch your staff"; never "block it" or "ensure it gets done" (only forgetting is genuinely prevented; late and said-done are surfaced sooner, never stopped: D82); never "SOP", "landlord", "units".

**What success looks like** (for leadership and business): carried from the Brief. At launch on the first real property: setup from the library without a blank box; an alert turned into work for named tenants; the right work reaching the right staff each morning on its own; a failed check handing the manager a ready-filled complaint; a staff member on a weak connection finishing with proof and not losing it; the owner seeing which properties keep the standard. Six months on: staff completion rate holds or rises (the honest test of the bet); managers run properties from the module, not their heads.

## 9. Vocabulary evidence

Filled from the product's own strings (the manager app's English strings and the manager web app) before any label appears in the map. A word with no evidence is a coinage and is marked as one.

Counted 2026-09-05 in the manager app's English strings (`lib/l10n/app_en.arb`, `local_en.dart`) and across the manager web app's source. Counts are occurrences, not screens; the example is one string the product shows.

| Word the map uses | Where the product already says it | Standing |
|---|---|---|
| checklist | web: 750, e.g. "Move-in/Move-out Checklist Status" | product's word |
| task, tasks | web: 4,123; "Pending Tasks" 60 | product's word |
| schedule | web: 9,768 | product's word |
| repeating task | not a UI string; "routine" appears 0 times in either app | **"routine" is a coinage.** The map says "repeating task" and names "routine" once as the word some of our docs use |
| staff | web: 1,425, "Staff" | product's word |
| warden | web: 1,528, app: "Adding Warden..." | product's word for the on-site role; the map uses "manager" per the locked vocabulary and says once that the app calls this person a warden |
| manager, owner | app: "Property Manager", "Owner"; web: thousands | product's words |
| housekeeping, cleaning | web: "Housekeeping Supervisor", `room_cleaning` | product's words |
| guard | web: 1,868, "Guard" | product's word |
| team member | app: "No team member found"; web: "Team Member" | product's word |
| assign, assignee | app: "Assign Business Team"; web: "Assignee" | product's words |
| category, priority | app: "Add $category Dues"; web: "Priority" | product's words |
| overdue, reminder | web: "overdue"; app: "Payment Reminder" | product's words |
| room, bed, floor, common area | app: "Each Room", "Total Beds", "Floor Map"; web: "Common Area" | product's words |
| move-out, check-in | web: "Move-Out", "early check-in" | product's words |
| complaint | app: "Type to search complaint"; web: 10,680 | product's word (never "ticket" in the map) |
| to-do | web: "To-do" 12 | product's word for F7 (self-kept tasks); "my tasks" appears 0 times, so the map says "your own to-dos" |
| proof | web: 322, e.g. "Proof of Business" | exists, in a different sense; the map keeps "proof" because sentence 7 locks it |
| inspection, audit | web: "Move-In Inspection Checklist", "flagged for audit review" | product's words |
| approve, reject, submit | web: "Approved", "Rejected"; app: "Submit" | product's words |
| library, template | web: `SampleLibraryModal`, "agreement-template" | product's words |
| needs attention | web: 9 | exists; the map uses it only for the home-screen feed, as that pack does |
| runner | not a UI string | **internal word** for the staff-side web page a task link opens. The map says "the task page on the phone" and names "runner" once in brackets for engineering |

## 10. Calls for Sanchay

Each is a call, not a fact. Recommendation first.

1. **The spine test.** Proposed: spine = removing it makes one of the twelve sentences false (25 items); supporting = everything else in bands B and C (25 items). Alternative: spine = bands A and B as they stand (43 items). Recommendation: the sentence test, because it gives a stakeholder a reason they can retrace without the band vocabulary.
2. **F8 (link a task to a real thing) as supporting, not spine.** D68 ruled the link not ship-blocking and moved F8 to Band B for disputes and handovers. Recommendation: supporting, named as the thing a dispute needs.
3. **F48 (manage routines) on the spine.** Sentence 8 says a property runs on a system; a routine nobody can check or stop is not a system. Recommendation: spine, even though it sits in stage 7.
4. **The eight moments, in that order.** Alternative: organise by the three jobs (tell, prove, see). Recommendation: moments, because tell, prove and see each appear in several moments and the reader lives in moments.
5. **The foundation as its own section, after the moments.** Alternative: fold each foundation item into the moment it protects. Recommendation: its own section, written for a non-engineer, because none of it is something a person meets.
6. **Word: "routine" for a repeating task.** The requirements use both "repeating task" and "routine". The evidence table decides; if the product never says "routine", the map says "repeating task".
7. **Length.** Target under 3,000 words, tables where structure is parallel, a how-to-read header. TAR-06's depth per item, not its length.

## 11. Rulings

Filled when Sanchay rules. Format: date · call number · ruling · what changed.

- 2026-09-05 · calls 1 to 7 · Sanchay: "go on both, strip the code and write the map" · all seven picks taken as ruled; the map was written once. Same message ruled that the requirements, brief and build sequence carry no code citations (moved to reference/grounding-notes.md section 8).
