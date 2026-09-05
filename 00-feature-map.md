---
title: "The Task module, as a property lives it"
date: 2026-09-05
version: "1.0"
owner: "Sanchay"
status: "current; written once from D1 to D85 and F1 to F59, after the sorted inventory was ruled on"
tags: [rentok, tasks, feature-map]
---

# The Task module, as a property lives it

## What is in here

The whole redesign in plain words, for anyone at RentOk: what the module does, for whom, moment by moment through a property's week; which pieces are the spine and which support it; what is parked and what brings it back; what we chose not to build and why; the sentences to use when talking about it; what success looks like. Ten minutes. It is requirements, not a build plan: nothing here says when or how much. For the one-line list of everything, read [00-feature-list.md](00-feature-list.md). For the argument in full, read [01-brief.md](01-brief.md). For the reasons behind any decision, the decision log [CHANGELOG.md](CHANGELOG.md) wins over this page.

## Contents

- [1. Why this exists](#1-why-this-exists)
- [2. The people in it](#2-the-people-in-it)
- [3. How to read the moments](#3-how-to-read-the-moments)
- [4. Setting the property up](#4-setting-the-property-up)
- [5. The morning](#5-the-morning)
- [6. Doing the work](#6-doing-the-work)
- [7. When something is wrong](#7-when-something-is-wrong)
- [8. The manager's day](#8-the-managers-day)
- [9. The owner's week](#9-the-owners-week)
- [10. When people change](#10-when-people-change)
- [11. The foundation nobody sees](#11-the-foundation-nobody-sees)
- [12. Five things that hold it together](#12-five-things-that-hold-it-together)
- [13. Parked, and what revives each](#13-parked-and-what-revives-each)
- [14. Not building, and why](#14-not-building-and-why)
- [15. How we talk about it](#15-how-we-talk-about-it)
- [16. What success looks like](#16-what-success-looks-like)
- [17. How it arrives](#17-how-it-arrives)
- [18. The sentence to end on](#18-the-sentence-to-end-on)

## 1. Why this exists

Work at a property fails three ordinary ways. Someone forgets. Someone does it late. Someone says it is done when it is not. None of that is unusual, and none of it is what hurts. What hurts is that nobody finds out until it has become a complaint from a tenant or a room that stays empty. The person who would report the miss is the same person whose memory dropped it.

We measured the cost against RentOk's own complaint records (the working is in [01-brief.md](01-brief.md) and decision D83). Of every complaint tied to a room, 31.5 percent is a repeat of the same kind on the same room within seven days, and for maintenance it is 33.4 percent. The room was not fixed the first time, and the second complaint is how the manager learned that.

Today a property runs on its manager's memory and her morning of telling people what to do. The module makes it run on a system instead: the work is written down once, arrives on its own, is proved by the person who did it, and is seen by each level above. That is the whole bet, and it is the sentence everything else hangs from: **a property runs on a system, not on one person's memory**, so it survives the manager's day off and the staff changing.

Only forgetting is genuinely prevented. Late and said-done are surfaced sooner, never stopped. We do not write "ensure it gets done" anywhere, because no software can.

## 2. The people in it

| Person | Who they are | What the module is to them |
|---|---|---|
| The owner | Runs several properties, rarely on any one. Today he trusts and hopes | One view of which properties keep the standard, without phoning managers |
| The manager | On site every day (the app calls this role a warden). Assigns the cleaning, chases the paperwork, takes the 4pm call. Reads Hindi first | Her morning back, and a record that protects her when the owner asks |
| Staff | Housekeeping and maintenance. Change jobs often. A cheap phone, a weak signal, a basement | Their own list, their own proof, nothing that feels like being watched |
| The guard | Mans the gate, does the night rounds. His paper register is his job and his dignity | Rounds as tasks, recorded for him if he has no smartphone. His register stays his |

## 3. How to read the moments

Sections 4 to 11 walk a property's week in the order people meet the module. Each row is one feature, with its label (F for feature, P for prerequisite, M for migration, the same labels every other doc uses), what happens, and why it is there. **Spine** means removing it makes one of the module's twelve locked sentences false (they are in section 15). **Supporting** means it strengthens the spine, and the row names what it protects. **Foundation** means nobody asks for it and nothing above works without it; it is invisible on purpose. Everything on this page is ruled in. Fourteen of the items below are ruled in but not yet placed in a stage, so nothing can be promised on their timing; they are listed together under "In no stage yet" in [00-feature-list.md](00-feature-list.md). What is parked or cut comes after the moments.

## 4. Setting the property up

The manager sits down once. She should not face a blank box, and she should be able to say what repeats, where, and who shares it, in her own language.

| Item | What happens | Why it is here |
|---|---|---|
| F9 | A library of starter checklists, suggested by property type and which modules are on. She copies one and edits it, or starts blank | Spine. A blank box is where a busy manager stops |
| F13 | The starter checklists come in Hindi and English. She writes her own in whatever script she uses | Spine. English-only starters mean rewriting every one before staff can use them |
| F5 | A repeating task on a real cadence: daily, weekly, monthly, chosen weekdays, a chosen date each month | Spine. Alternate-day cleaning and the 1st-of-month reading cannot be said otherwise |
| F10 | Scoped to the whole property, a floor, chosen rooms, or all rooms in one tap, with the room picker a complaint uses | Spine. Otherwise every task covers everything and no room is anyone's |
| F16 | Several people share a task: pooled (any one finishes it for all) or one-each (everyone gets a copy). She picks; nothing guesses | Spine. "Someone clean the lobby" and "each guard does his round" are different things |
| F32 | A category, a priority, a description, and for repeating work an end date. Categories are RentOk's plus her own, suggested as she types | Spine. The one list has nothing to filter or sort by otherwise |
| F49 | Before she switches a repeating task on, she sees how many rooms and people it will reach | Supporting. Stops a repeating task shared across every room (F5, F16) becoming a 200-tasks-a-day flood |
| F33b | One task assigned across many rooms or people at once | Supporting. Keeps room-by-room scope (F10) and shared assignment (F16) usable at 200 rooms, instead of 200 saves |
| F47 | A new property starts with its repeating tasks already set up, unassigned; an existing one is offered them | Supporting. Keeps the starter library (F9) from being a blank box on day one. Unassigned, so a property with no staff yet gets no failures |
| F27 | Creating a task is built as something other parts of RentOk can call, not only a form, so the assistant can create one later | Supporting. Keeps the door open for the assistant (sentence 11). A build rule on every creation path, not a screen |

## 5. The morning

Nobody has to remember to start the day. The work arrives, each person sees their own, and the manager sees everything in one place.

| Item | What happens | Why it is here |
|---|---|---|
| P1 | Repeating work fires on its own, from a scheduler that is found, owned and locked | Foundation. Every repeating feature stands on it |
| P0 | A repeating task with nobody assigned creates no work, instead of failures against no one | Foundation. The manager's view of it (F48, "not running, nobody assigned") comes with the on-ramp |
| F40 | Four moments: one morning message carrying all the day's links, then nudges with a count and one link. Never one message per task | Spine. Unbatched, WhatsApp blocks the number and staff mute the only channel |
| F17 | Each staff member sees their own tasks and their own record, and only those | Spine. The proof belongs to the person who collected it |
| F20 | One list: work the system raised, work a person assigned, her own to-dos. Filtered by category, sorted by due date then priority | Spine. Three places means she checks none |
| F57 | Work the system raises carries the same categories as everything else | Spine. Otherwise the one filter breaks for a third of the list on day one |
| F43 | A free tag on a task, for grouping work that maps to no room or tenant | Supporting. Keeps the one list (F20) filterable for work that maps to no room or tenant |

## 6. Doing the work

A cleaner in a basement on a cheap phone, with a signal that comes and goes. The task page on the phone (engineering calls it the runner) has one job: let her prove the work without losing it.

| Item | What happens | Why it is here |
|---|---|---|
| F11 | She does the task on the task page, with proof: photos, answers, time, signature, and location checked against the property (flagged, never blocking) | Spine. No evidence the work happened is no product |
| F12 | Half-done work survives a closed tab, a dropped signal and a phone restart | Spine. Losing ten items to a dropped signal means she does not start again |
| F46 | A photo question opens the camera, never the gallery, with no setting to turn that off | Spine. One gallery upload makes every photo in the system worthless as evidence |
| F39 | Photos shrink on the phone before upload; the local copy is deleted after | Supporting. Keeps proof (F11) possible on 2G and the phone's storage clear |
| F29 | The question types a real inspection needs: choices, ratings, numbers with units, dates, several photos, instructions, and items shown only after an earlier answer | Spine. Without them an inspection cannot say "not applicable", give a score, or record a meter |
| F30 | Per item: required, a photo required alongside, a reference picture, a note under the label, sections | Spine. "Required" must mean something, or the one photo a dispute needs is the one skipped |
| F41 | The page knows who is submitting from the task itself, never from the phone; a link stops working when its window closes; no login | Foundation. Otherwise anyone with a link files work as anyone |
| F15a | "Couldn't do it", with a reason: the tenant was asleep, the room was locked | Supporting. Keeps each person's own record (F17) honest. Blocked work must not look like ignored work |
| F33a | She passes a task on, including the guard going off duty at 10pm handing his open rounds to the next shift | Supporting. Keeps each person's own record (F17) fair. Otherwise he wakes the manager or goes overdue against himself |
| F53 | The manager records work done by someone without a smartphone | Supporting. Keeps each person's record (F17), the insight numbers (F21) and the exception view (F22) honest. The guard's rounds must not read as failures |
| F59 | A visit log: a task that records arriving and leaving, for staff who travel between buildings | Supporting. Makes proof (F11) work for operators whose supervisors cover many buildings |
| F15b | A voice note, for the person who explains better than they type | Supporting. Gives "couldn't do it" (F15a) and comments (F14) a voice for people who do not type |
| F7 | Her own to-dos with reminders, and to-dos she sets for the people she leads, in the same list as everything else | Spine. The third source of work: what she owes herself |

## 7. When something is wrong

An inspection finds a broken tap. The person who found it must not be the one punished for it, and the tap must not die in a form.

| Item | What happens | Why it is here |
|---|---|---|
| F31 | A single item carries a problem. The task is still done; the problem is a separate thing | Spine. Otherwise reporting a fault fails the reporter, so faults stop being reported |
| F2 | A reported problem hands over a complaint pre-filled with room, item and photo, raised with one tap. A person raises it; nothing raises itself | Spine. The single thing S2L, a fifty-building customer, is waiting for |
| F54 | When she raises that complaint, the open ones for that room show first | Supporting. Keeps the one-tap complaint (F2) from turning one leaking tap into seven complaints |
| F18 | Every task has a due date, and a due time where the manager sets one. Late is late; a reminder goes out; a miss escalates | Spine. Otherwise nothing is ever late and a missed day disappears |
| F45 | The server decides what is late, never the phone's clock, and several missed reminders arrive as one message | Foundation. A phone with a wrong clock cannot create a false overdue |
| F24c | One occurrence of a repeating task is skipped or moved, for a festival or a clash, without switching the whole thing off | Supporting. Keeps a repeating task (F5, F48) alive through a festival instead of switched off and forgotten |

## 8. The manager's day

She is walking the floors, not sitting at a desk. What she needs is fast, on the phone, and never a second copy of what she already knows.

| Item | What happens | Why it is here |
|---|---|---|
| F4 | A one-off task, any time: who, when, what. It needs no checklist. "Fix the gate light" is a task | Spine. Otherwise anything unplanned goes back to WhatsApp |
| F24a | She captures that one-off on the spot, on her walk-round | Supporting. Makes the one-off task (F4) reachable on a walk-round. Otherwise she writes it on her hand |
| F58 | An alert RentOk already raises, such as twelve tenants overdue, becomes work: one task per item, each with an owner. She decides who | Spine. The alert can show a problem; this makes it work someone owns |
| F19 | Review, where she has switched it on: approve, reject with a reason, or send back. Daily cleaning is done when submitted; an audit is reviewed | Spine. Otherwise submitted and done mean the same thing |
| F24b | Approve and reject from the phone | Supporting. Makes review (F19) happen on the floor, not at a desk |
| F14 | A comment thread on a task, with tagging | Supporting. Gives review (F19) a place for the reason. When work fails, the reason must reach the record |
| F48 | Her repeating tasks in one place: list, edit, pause, resume, archive, and what each has produced | Spine. A repeating task she cannot check or stop is not a system, it is a trap |
| F50 | A repeating task whose work is only ever ignored pauses itself and tells her | Supporting. Keeps repeating tasks (F48) and the list clear of a forgotten one |
| F22 | Her exception view: what needs attention today at her property, the same view the owner sees of it | Spine. She must never be blindsided on a call by a list she has never seen |

## 9. The owner's week

He opens one screen on Sunday night. He wants to know which properties keep the standard, and he does not want to phone anyone to find out.

| Item | What happens | Why it is here |
|---|---|---|
| F22 | The exception view across his properties: what needs attention, where, since when | Spine. Getting him out of asking managers how things are going is what he pays for |
| F21 | The first insights: completion, on-time rate, problems by room, week over week. Never a league table of people | Supporting. Deepens what the owner sees (F22); the exception view is the promise |
| F8 | A task is linked to a real thing: a room, a tenant, a due, an asset. Everything ever done to that thing is one history | Supporting. Gives proof (F11) a home per room and tenant. "What was done to room 204 this year" is a dispute's question |
| F1 | A task tied to a real thing shows its live state (paid or not) and never changes it or decides the task is done | Supporting. Keeps the link (F8) honest, sentence 2 made visible; the person still decides |
| F25a | A weekly digest over WhatsApp | Supporting. Nudges him to open the exception view (F22); not a second view |
| F25b | Export the task record of a room, a person or a month, for an audit or a dispute | Supporting. Turns the record (F11, F8) into something an auditor can hold. Proving a year of compliance must not mean screenshots |

## 10. When people change

Staff leave every month. A manager leaves sometimes. A new property starts. None of it should orphan work or erase what was done.

| Item | What happens | Why it is here |
|---|---|---|
| F51 | When someone leaves, their open work returns to the manager to reassign or close. Their submitted proof keeps their name forever | Supporting. Keeps the record (F17, F21, F22) true. In a business built on churn, otherwise every resignation orphans work |
| F52 | When a manager is deactivated, her repeating tasks and her pending reviews pass to a successor; nothing fires under a dead account | Supporting. Keeps her repeating tasks (F48) and pending reviews (F19) alive. Rare where staff leaving is constant, hence supporting |
| F3 | A finished move-out creates the room-prep task on its own. The work then belongs to a person | Spine. The one event we know creates work. Otherwise an empty room waits on memory |
| M1 | Room cleaning, the building's most common task, becomes an ordinary repeating task pooled per room; every cleaning records who cleaned, a miss is the team's | Foundation. Without it the most common task in the building has no doer on record |

## 11. The foundation nobody sees

None of this is a feature anyone asks for. All of it is what makes the record worth having. Most of it ships first and is invisible on purpose; the checklist-shape change comes with the question types, the category mapping sits with the one list, and the account-by-account switch-on applies to every stage.

| Item | What must be true |
|---|---|
| F26 with M2 | Five permissions: see, see only my own, create and assign, review, archive. Managers keep theirs; staff who cannot manage the team see only their own |
| F36 | Every submission is checked against the checklist it belongs to before it is stored. A wrong shape is refused, listing every failing item at once |
| F44 | A checklist with open tasks cannot be changed under the people using it; she saves it as a new copy |
| F37 | Every change to a task, a checklist or a schedule is logged: who, what, when. A submitted record cannot be quietly altered |
| F38 | Nothing is deleted. Tasks, checklists and schedules are archived and can be brought back |
| M5 | Three question types that live checklists use under other names are given their real names, before validation would reject nearly half of them |
| M6 | A checklist can hold sections, and items that appear only after an earlier answer |
| M3 | Work the system raises is mapped onto the shared categories |
| M4 | Released to everyone, switched on account by account, so the notification volume and the access change can be watched |

## 12. Five things that hold it together

Five of the twelve locked sentences in section 15 do the structural work; these are the ones a feature can break. They are the reason the pieces make one product rather than a list.

1. **Nothing acts on its own; a person decides.** No task closes itself, no complaint is raised without someone raising it, no alert becomes work without someone turning it into work.
2. **The proof belongs to the person who collected it.** Their defence first, the record second. No fines, no scorecard, this cycle or next, for staff or for managers.
3. **A task tied to a real thing shows that thing's live state and never writes into it.** A task on a rent due shows paid or unpaid; it never marks the due paid and never decides the task is finished.
4. **Three sources, one list.** The system raises it, a person assigns it, or a person keeps it for themselves, and the manager sees all three in one place.
5. **Move-out is reused, not rebuilt.** It is already a task that records deposit deductions. It is the pattern for a task that creates work.

## 13. Parked, and what revives each

Good ideas with the wrong dependencies. Each names what brings it back, so "later" is a condition, not a shrug.

| Parked | Revived when |
|---|---|
| The business creates tasks from its other events, beyond move-out | Cross-module hooks exist. Move-out is in now as F3 |
| A repeating task that watches a condition and creates work on its own | We have watched an operator want one. Three of the five it was meant for turned out to be events, not conditions |
| Lobby, lift and stairs as pickable places with their own history | The next release; tags carry it until then |
| Building tasks by talking to the assistant | The RentOk AI work lands; task creation built as something the assistant can call (F27) keeps the door open |
| Scoring a checklist from its ratings | The release after next |
| Scanning an asset's code, and conditions on items beyond the branching that is in (an item shown after an earlier answer) | The release after next. How much of the old "conditional items" idea the branching already covers is Sanchay's to say |
| Push notifications, email, a dedicated inbox | The release after next. WhatsApp first by decision, not omission |
| Every system-raised task the home screen could show, all 65 | Its own backend issue; the pack is in this repo under reference |
| Drafts kept on the server, beyond the phone | The next release |
| Meter reading as a route: many stops, each with a photo, monthly | The release after next; needs a task target that is not a room or a property |
| Approval chains, delegation, quiet hours, work orders, shared devices, one task linked to many things at once | The release after next, each on its own evidence |

## 14. Not building, and why

Each of these someone will argue for. Each is out because a person does not need it, not because it is hard.

| Not building | Why |
|---|---|
| Fines, salary deductions, or a staff scorecard, for anyone including managers | The moment staff believe the tool can cost them money they stop filling it honestly, and the proof collapses for the owner too |
| Anything that acts without a person confirming | A wrong automatic action on money or on a complaint is worse than a missed manual one |
| A free-form if-this-then-that rule builder | A developer tool, not an operator tool. Open-ended power comes through the assistant, later |
| High-frequency logs, such as a motor's on and off times | That is telemetry, not work. A due date is the wrong shape for it |
| Tracking licence and certificate expiry dates | A renewal is a repeating task with a date the manager sets. We do not watch expiries for her |
| Attendance and shift clocking | A different product. Folding it in blurs what this module is for |
| A native Task tab in the mobile app | The task pages reach the app through the existing web view |
| Rebuilding move-in and move-out | Move-out already records deposit deductions correctly. Reuse it |
| The guard's visitor register | His paper register is his job and his dignity. Position the app as replacing it and he resists |

## 15. How we talk about it

For anyone describing the module outside product: marketing, sales, support, a demo. These are the twelve locked sentences in short form; the exact wording, to be used verbatim in anything written, is in [CHANGELOG.md](CHANGELOG.md#the-locked-sentences).

1. Nothing acts on its own; a person decides.
2. A task tied to a real thing shows that thing's live state; it does not write into it, and it does not judge whether the work is done.
3. Linking is for context, filtering, navigation and history, not for driving completion.
4. A task and a complaint linked to it run on separate statuses.
5. Three sources of task, one place.
6. The module's jobs, in order: tell people what to do, prove it was done, let each level see and help.
7. The proof belongs to the person who collected it. No fines, no scorecard.
8. A property runs on a system, not on one person's memory.
9. An alert can be turned into work: one task per item, with an owner and a record.
10. A task for several people is done by any one (pooled) or by each one separately (one-each); the creator picks; who did what is recorded.
11. Open-ended power comes through the assistant later.
12. Move-out is an existing task; reuse it, do not rebuild it.

**Never say:** watch your staff, catch, surveillance, fines, scorecard, block it, ensure it gets done. Only forgetting is prevented; late and said-done are surfaced sooner. **Never use** SOP, landlord, or units; say checklist, owner, and rooms or beds. The people are staff, manager, owner and guard.

## 16. What success looks like

At launch, on the first real property: the manager sets up from the library without a blank box; a rent-overdue alert becomes work for three named tenants; the right work reaches the right staff each morning on its own; a failed check hands her a ready-filled complaint and she raises it; a cleaner on a weak signal finishes with proof and loses nothing; the owner opens one view and sees which properties keep the standard.

Six months on: the share of assigned tasks staff finish holds or rises, which is the honest test of the bet; managers run their properties from the module instead of their heads; owners hold managers to a standard they can finally see; and the on-time rate, the number that moves before a complaint does, is climbing.

## 17. How it arrives

Seven stages, each a point you could stop at and still have something whole. The first makes what exists safe and is invisible; the second is the first thing a manager sees. Every item on this page sits in one of them or is waiting for the line to be drawn. The order and what depends on what are in [03-build-sequence.md](03-build-sequence.md); the one-page list by stage is [00-feature-list.md](00-feature-list.md); price and status live in Linear.

## 18. The sentence to end on

A property runs on a system, not on one person's memory. Everything on this page either writes the work down once, makes it arrive on its own, proves it by the hand that did it, or lets the next level see. Anything that does none of those four is not on this page.
