---
title: "Task Module — Build Sequence and Break Points"
date: 2026-08-04
owner: "Sanchay"
status: "draft — needs engineering's cost before a line is drawn"
tags: [rentok, tasks, sequencing, cut]
---

# Build Sequence and Break Points

[feature-requirements.md](feature-requirements.md) says **what** is needed and in what order it should
survive a cut. It does not say what to build first, and the two are not the same thing — a cut order
ranks by user pain, a build order has to respect what physically depends on what.

This document does the second job. **It contains no estimates.** Nothing here says how long anything
takes; that is engineering's, and a sequence proposed without it would be a guess dressed as a plan.

## How to use it

Seven stages. Each one is a **real ship point** — you could stop after any of them and have something
coherent, not a half-built layer. Each stage says three things:

- **What ships**
- **What the user can now do that they could not before**
- **What they still cannot do** — the honest cost of stopping here

Engineering prices the stages and draws the line wherever capacity actually lands. The line does not
have to fall on a stage boundary, but if it falls inside one, read that stage's "still cannot do" as
partly true and check the dependency list before splitting it.

---

## The dependency map

Only real blockers are listed — X genuinely cannot work, or cannot be honest, without Y. Everything
else can be built in any order.

| This | Cannot ship without | Why |
|---|---|---|
| Anything recurring (F5, F18, F40, F48, F49, F50, F24c) | **P1** | There is no scheduler in the repo. The trigger endpoint is open and depends on an unidentified caller. |
| **P0** (skip unassigned routines) | **M1** | Room cleaning deliberately creates unassigned tasks today. Skipping first stops cleaning dead. |
| **M1** (cleaning becomes a normal task) | F10, F16 | It needs all-rooms scope and pooled assignment to exist. |
| **M2 / F26** (permissions) | — | Pairs with M1 (D69) so the staff default lands when cleaning already has assignees. |
| F41 (runner identity) | F16 | Identity comes from the task record; a pooled task has none, so it needs the name tap. |
| F36 (server validation) | F44 | Validation needs a checklist that cannot change under it. |
| F11 (runner with proof) | F29, F30, F46, F39 | The runner renders question types and per-item settings. |
| F12 (partial save) | F11 | Nothing to save otherwise. |
| F31 (an item carries a problem) | F29, F30 | Needs an item that can be marked failed. |
| F2 (problem → complaint) | F31 | The failed item is what raises it. |
| F54 (show open complaints first) | F2 | — |
| F19 (review) | F11 | Nothing to review. |
| F21 (insights) | F18, F16, **F53** | On-time needs due dates; per-person needs fan-out; without F53 the numbers are false for anyone with no smartphone. |
| F22 (exception view) | F21's data, **F53** | Same falseness, seen by the owner. |
| F20 (one list) | F32, F57 + M3 | Filter and sort need a category on all three sources. |
| F58 (alert → work) | F4, F8, F20 | It creates a task per item and links it. |
| F8 (link + history) | F11 | History shows submitted answers, so answers must exist. |
| F48 / F49 / F50 / F24c | F5 | They manage repeating tasks. |
| F3 (move-out → prep task) | F4 | It creates a task. Independent of the scheduler. |
| F7 (my tasks) | F4, F18 | — |
| F40 (notifications) | F18 | Nudges are relative to a due time. |
| F45 (server decides late) | F18 | — |
| F13 (bilingual templates) | F9 | Templates must exist to be written twice. |
| F9 (library) | F29, F30 *(soft)* | See the open sequencing questions below — it could ship on today's five question types. |
| F17 (staff see own) | F26 | It is a permission. |
| F53, F15a, F33a | F4 / F11 | They are outcomes and actions on an existing task. |

---

## Stage 1 — Make what already exists safe

Nothing new for the user. The module today runs scheduled checklists and takes submissions through an
open link with no permissions, no validation, no audit log and no expiry. This stage fixes the module
we already shipped.

**Ships:** P1 · F26 + M2 · F41 · F36 · F44 · F37 · F38 · F46 · F45

**The user can now:** nothing they could not before. **This stage is invisible on purpose.**

**They still cannot:** give work to a named person, set a real cadence, be chased, or prove anything
beyond a tick.

**Why it is first:** every stage below writes records. Records written before this stage are the ones
we would have to defend later and could not. P1 in particular — today's recurring work depends on a
caller nobody has identified, which means it can stop without anyone knowing.

---

## Stage 2 — Give the work an owner and a real cadence

**Ships:** F10 · F16 · F5 · F4 · F32 · **M1 + P0**

**The user can now:** create a one-off task and give it to a named person. Set cleaning to Mon/Wed/Fri
instead of daily. Scope work to a floor or a chosen set of rooms. And after M1, **every room finally
has a recorded doer** instead of one shared link for the whole team.

**They still cannot:** be chased — nothing is ever late and nothing notifies. No proof beyond a tick.
No visibility above the manager.

**Why here:** this is the first stage a manager notices, and M1 is the single biggest change to the
most frequent task in the building.

---

## Stage 3 — Make it chase itself

**Ships:** F18 · F40 · F24c · F33a · F15a · F53

**The user can now:** work arrives on the phone each morning, gets nudged before it is due, and is
recorded as late if it is not done. A guard hands his open work over at 10pm without waking anyone. A
festival day is skipped instead of the routine being switched off. Someone who could not do a job says
why. The guard with no smartphone stops being permanently late.

**They still cannot:** prove any of it with a photo. No review, no insight, no owner view.

**Why F24c, F33a, F15a and F53 are here and not later:** D67. The moment F18 exists, every one of these
is the difference between a true record and a false one. Ship F18 without them and the module starts
accusing people in week one.

---

## Stage 4 — The proof

**Ships:** F29 · F30 · F11 · F39 · F12 · F17

**The user can now:** collect real evidence as part of the work — photo from the camera, timestamp,
location, signature. Work survives a dropped signal. **Every staff member sees their own record**, which
is the first stage where the bet is actually built rather than promised.

**They still cannot:** turn a fault into a ticket. No review. No manager or owner view.

---

## Stage 5 — The fault loop

**Ships:** F31 · F2 · F54

**The user can now:** an inspection that finds a broken light raises a complaint with one tap,
pre-filled, routed by the complaint module's existing categories — and the person who reported it does
not get a failed task for having reported it. A fault that keeps failing joins the existing complaint
instead of making a seventh.

**They still cannot:** review anything. No insight, no owner view, no library.

**Why here:** this is S2L's stated top ask, and it needs the runner (stage 4) to exist first.

---

## Stage 6 — Letting each level see

**Ships:** F19 · F20 · F57 + M3 · F8 · F21 · F22

**The user can now:** the manager approves, rejects with a reason, or sends work back. One list holds
all three sources under one filter. *"What has been done to room 204 this year"* has an answer, with the
submitted photos. She sees what is failing and where. **The owner sees exceptions across every property
— and the manager sees the same list about hers, at the same time (D71).**

**They still cannot:** start from anything but a blank box. Nothing runs itself.

**Why F53 is a hard dependency here:** F21 and F22 both report failures. Without F53 they report failures
that never happened, about the one person who cannot argue back.

---

## Stage 7 — The on-ramp and the things that run themselves

**Ships:** F9 · F13 · F48 · F49 · F50 · F7 · F3 · F58

**The user can now:** set up from a library instead of a blank box, in Hindi as well as English. See,
edit, pause and stop her routines, and see what each has produced. Keep her own follow-ups. A finished
move-out creates the room-prep task on its own. An alert on the home screen becomes assigned work with
one action.

**This is the stage where D80's moat starts accruing** — nothing accumulates until routines exist, and
the library is what makes them exist.

---

## Three sequencing calls that are genuinely arguable

These are the places I would expect engineering or you to move something, and they are judgement, not
dependency.

**1. F9 (the library) is in stage 7 but D80 calls it the highest-leverage item in the set.**
It sits late because it is listed as depending on F29/F30 — richer question types to build templates
with. **But it could ship on today's five question types and gain the richer ones later**, which would
move it to stage 2 or 3, where it starts the moat accruing months earlier. It is also largely *content*
work (the Checklist Library project), which may not compete for the same engineers as everything else
here. **Worth deciding deliberately rather than inheriting from the dependency list.**

**2. F51 (someone leaves, their work returns) is in Band C but churn is called "the whole game".**
D25 calls staff departure *"the most common event in the system."* It is not in any stage above because
it is Band C. If churn really is the defining event, it belongs in stage 3 with the other
record-honesty items — a resignation currently orphans open work silently.

**3. F58 (alert → work) is in stage 7 but nothing technical holds it there.**
It needs F4, F8 and the existing feed. F8 lands in stage 6, so F58 could be the first thing in stage 7
or the last thing in stage 6. It is also the most demo-able single feature in the whole set, which
matters if any stage has to be shown to someone.

---

## What is in no stage

Band C, minus the two called out above: **F14** (comments) · **F15b** (voice notes) · **F24a** (quick
capture) · **F24b** (approve from phone) · **F33b** (bulk assign) · **F52** (manager deactivated) ·
**F47** (starter routines) · **F25a** (owner digest) · **F59** (visit log) · **F1** (linked-thing live
state) · **F43** (tags) · **F25b** (export).

**F27 is not a stage — it is a constraint.** "Creation is a callable action, not a form-only path"
applies to F4, F5 and F58 as they are built. Retrofitting it later means writing them twice, so it
should be a build rule from stage 2 rather than a line item at the end.

---

## What this needs before a line can be drawn

1. **Engineering's cost per stage** — Nimit and Jatin. Without it this is an order, not a plan.
2. **P0 and P1 confirmed** — P1 especially: nobody currently knows what calls the trigger endpoint, and
   every recurring stage sits on it.
3. **D69's permission proxy confirmed** — `view_team` / `add_team` / `edit_team` is the closest existing
   signal for "can hand out work", but it is a stand-in, not a real flag.
4. **A decision on the three arguable calls above**, especially F9.
