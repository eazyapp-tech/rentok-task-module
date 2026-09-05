---
title: "Task Module — Build Sequence and Break Points"
date: 2026-08-04
owner: "Sanchay"
status: "draft — needs engineering's cost before a line is drawn"
changelog: "2026-08-04 — F9, F13, F29, F30 moved from stage 7 to stage 2; F51 moved from Band C into stage 3 (Sanchay). Fault loop noted as unblocked from stage 3."
tags: [rentok, tasks, sequencing, cut]
---

# Build Sequence and Break Points

[02-requirements.md](02-requirements.md) says **what** is needed and in what order it should
survive a cut. It does not say what to build first, and the two are not the same thing — a cut order
ranks by user pain, a build order has to respect what physically depends on what.

This document does the second job. **It contains no estimates.** Nothing here says how long anything
takes; that is engineering's, and a sequence proposed without it would be a guess dressed as a plan.

## What is in here

Seven stages, each a real ship point, with what ships, what a person can then do, what they still cannot, and the dependency map of what genuinely cannot ship without what. For engineering pricing the stages and for anyone asking "what arrives when". It contains no estimates by design; those live in Linear. It is not the spec: stages 1 and 2 are specified in [04-spec-stages-1-2.md](04-spec-stages-1-2.md).

## Contents

- [How to use it](#how-to-use-it)
- [The dependency map](#the-dependency-map)
- [Stage 1: Make what already exists safe](#stage-1--make-what-already-exists-safe)
- [Stage 2: Give the work an owner, a real cadence, and something worth filling in](#stage-2--give-the-work-an-owner-a-real-cadence-and-something-worth-filling-in)
- [Stage 3: Make it chase itself](#stage-3--make-it-chase-itself)
- [Stage 4: The proof](#stage-4--the-proof)
- [Stage 5: The fault loop](#stage-5--the-fault-loop)
- [Stage 6: Letting each level see](#stage-6--letting-each-level-see)
- [Stage 7: The on-ramp and the things that run themselves](#stage-7--the-on-ramp-and-the-things-that-run-themselves)
- [Three sequencing calls that are genuinely arguable](#three-sequencing-calls-that-are-genuinely-arguable)
- [What is in no stage](#what-is-in-no-stage)
- [What this needs before a line can be drawn](#what-this-needs-before-a-line-can-be-drawn)

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
| Anything recurring (F5, F18, F40, F48, F49, F50, F24c) | **P1** | A scheduler runs today but nothing in our own code registers it. The trigger endpoint is open and depends on that unidentified caller. The job is to find it and authenticate it, not to build one — backend issue #6363. |
| **P0** (skip unassigned routines) | **M1** | Room cleaning deliberately creates unassigned tasks today. Skipping first stops cleaning dead. |
| **M1** (cleaning becomes a normal task) | F10, F16 | It needs all-rooms scope and pooled assignment to exist. |
| **M2 / F26** (permissions) | — | Pairs with M1 (D69) so the staff default lands when cleaning already has assignees. |
| F41 (runner identity) | F16 | Identity comes from the task record; a pooled task has none, so it needs the name tap. |
| F36 (server validation) | F44 | Validation needs a checklist that cannot change under it. |
| F11 (runner with proof) | F46, F39 | Proof capture — verified time, location, signature. Question rendering is F29/F30 and lands earlier, at stage 2. |
| F12 (partial save) | F11 | Nothing to save otherwise. |
| F31 (an item carries a problem) | F29, F30 | Needs an item that can be marked failed. |
| F2 (problem → complaint) | F31 | The failed item is what raises it. |
| F54 (show open complaints first) | F2 | — |
| F19 (review) | F11 | Nothing to review. |
| F21 (insights) | F18, F16, **F53** | On-time needs due dates; per-person needs one-each; without F53 the numbers are false for anyone with no smartphone. |
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
| F9 (library) | F29, F30 | Resolved by moving all three to stage 2 — templates are authored once, against the full set of question types, rather than written on five types and rewritten later. |
| F17 (staff see own) | F26 | It is a permission. |
| F53, F15a, F33a | F4 / F11 | They are outcomes and actions on an existing task. |
| F51 (someone leaves) | F33a | Returning open work is a reassignment. |

---

## Stage 1 — Make what already exists safe

Nothing new for the user. The module today runs scheduled checklists and takes submissions through an
open link with no permissions, no validation, no audit log and no expiry. This stage fixes the module
we already shipped.

**Ships:** P1 · M5 · F44 · F36 · F26 + M2 · F41 · F37 · F38 · F46 · F45

*M5 added 2026-09-05 (it was decided with D84 on 5 Aug and never carried into this list): it renames three live question types found in 48.2% of checklists, and runs before F36 can reject unknown types.*

**The user can now:** nothing they could not before. **This stage is invisible on purpose.**

**They still cannot:** give work to a named person, set a real cadence, be chased, or prove anything
beyond a tick.

**Why it is first:** every stage below writes records. Records written before this stage are the ones
we would have to defend later and could not. P1 in particular — today's recurring work depends on a
caller nobody has identified, which means it can stop without anyone knowing.

---

## Stage 2 — Give the work an owner, a real cadence, and something worth filling in

**Ships:** F10 · F16 · **M1 + P0** · M6 · **F29 · F30** · F5 · F4 · F32 · **F9 · F13**

*M6 added 2026-09-05 (decided with D84 on 5 Aug, never carried here): the checklist structure gains sections and branching, and runs after M5 and before F29.*

**The user can now:** create a one-off task and give it to a named person. Set cleaning to Mon/Wed/Fri
instead of daily. Scope work to a floor or a chosen set of rooms. And after M1, **every room finally
has a recorded doer** instead of one shared link for the whole team.

And she starts from **a library rather than a blank box** — in Hindi as well as English — with
checklists that can express what an inspection actually needs: pass/fail/not-applicable, a rating, a
measurement, a required item, a reference picture, sections.

**They still cannot:** be chased — nothing is ever late and nothing notifies. Proof is still a tick and
a photo, without a verified time, location or signature. No visibility above the manager.

**Why here:** this is the first stage a manager notices, and M1 is the single biggest change to the most
frequent task in the building.

**Why the library and the question types moved here (decided 2026-08-04).** They sat in stage 7 by
dependency, which contradicted D80 — the library is the on-ramp, and **nothing accumulates until
routines exist**. Pulling F29 and F30 up removes the dependency properly, rather than shipping F9 on
today's five question types and rewriting the templates later. The moat starts accruing at stage 2
instead of stage 7.

**The trade, stated plainly:** stage 2 is now the biggest stage in the sequence, so **the time to first
user-visible value goes up.** If that matters, it splits cleanly at the seam — **2a** (F10, F16, F5, F4,
F32, M1+P0) is owner and cadence on today's checklists; **2b** (M6, F29, F30, F9, F13) is the library and the
richer types. 2a alone is still a coherent ship.

---

## Stage 3 — Make it chase itself

**Ships:** F18 · F40 · F24c · F33a · F15a · F53 · **F51**

**The user can now:** work arrives on the phone each morning, gets nudged before it is due, and is
recorded as late if it is not done. A guard hands his open work over at 10pm without waking anyone. A
festival day is skipped instead of the routine being switched off. Someone who could not do a job says
why. The guard with no smartphone stops being permanently late. And when someone quits, their open work comes back to the manager instead of going overdue against a person who has left.

**They still cannot:** prove it beyond a photo and a self-reported time — no verified timestamp, no location checked against the property, no signature. No review, no insight, no owner view.

**Why F24c, F33a, F15a, F53 and F51 are here and not later:** D67. The moment F18 exists, every one of these
is the difference between a true record and a false one. Ship F18 without them and the module starts
accusing people in week one.

---

## Stage 4 — The proof

**Ships:** F11 · F39 · F12 · F17

**The user can now:** collect real evidence as part of the work — verified timestamp, location checked
against the property, signature, photos compressed for a weak line. Work survives a dropped signal.
**Every staff member sees their own record**, which is the first stage where the bet is actually built
rather than promised.

*(F29 and F30 moved to stage 2. What remains here is proof capture, not question rendering — the runner
already renders questions today.)*

**They still cannot:** turn a fault into a ticket. No review. No manager or owner view.

---

## Stage 5 — The fault loop

**Ships:** F31 · F2 · F54

**The user can now:** an inspection that finds a broken light raises a complaint with one tap,
pre-filled, routed by the complaint module's existing categories — and the person who reported it does
not get a failed task for having reported it. A fault that keeps failing joins the existing complaint
instead of making a seventh.

**They still cannot:** review anything. No insight, no owner view, no library.

**Why here:** this is S2L's stated top ask.

**It is now unblocked earlier than it sits.** The fault loop depended on F29/F30 for an item that can be
marked failed — and those moved to stage 2. Nothing else holds it: F2 uses the complaint module that
already exists, and a failed item can carry a photo today without waiting for F11's signature and
location. **So this stage could run any time after stage 3, including before stage 4.** Left here because
proof is more foundational to the bet than any one customer's ask — but if S2L's timeline matters, this
is the cheapest thing to pull forward.

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

**Ships:** F48 · F49 · F50 · F7 · F3 · F58

**The user can now:** see, edit, pause and stop her routines, and see what each has produced. Keep her
own follow-ups. A finished move-out creates the room-prep task on its own. An alert on the home screen
becomes assigned work with one action.

*(The library moved to stage 2, so D80's moat now starts accruing there rather than here. What is left
in this stage is the work that runs without being asked.)*

---

## Three sequencing calls that are genuinely arguable

These are the places I would expect engineering or you to move something, and they are judgement, not
dependency.

**1. F9 (the library) — DECIDED 2026-08-04: moved to stage 2, and F29/F30 with it.**
It sat in stage 7 by dependency, contradicting D80's claim that the library is the on-ramp and that
nothing accumulates until routines exist. Rather than ship F9 on today's five question types and rewrite
the templates later, the question types move up too. The cost is that stage 2 becomes the biggest stage
and first user-visible value takes longer; the seam to split it at is written into stage 2. **A knock-on
worth noting: this unblocks the fault loop (stage 5) to run any time after stage 3.**

**2. F51 (someone leaves, their work returns) — DECIDED 2026-08-04: moved to Band B and into stage 3.**
D25 calls staff departure *"the most common event in the system"*, and F51 fails D67's test — open tasks
on someone who has left keep going overdue against them, so F21 and F22 report failures about a person
who no longer works there. It sits with the other record-honesty items and depends on F33a, which is
already in that stage.

**3. F58 (alert → work) is in stage 7 but nothing technical holds it there.**
It needs F4, F8 and the existing feed. F8 lands in stage 6, so F58 could be the first thing in stage 7
or the last thing in stage 6. It is also the most demo-able single feature in the whole set, which
matters if any stage has to be shown to someone.

---

## What is in no stage

Band C, minus the two called out above: **F14** (comments) · **F15b** (voice notes) · **F24a** (quick
capture) · **F24b** (approve from phone) · **F33b** (bulk assign) · **F52** (manager deactivated — the sibling of F51, left in Band C because a manager leaving is rare where staff leaving is constant) ·
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
3. **D69's permission proxy confirmed** — the three team-management permissions are the closest existing
   signal for "can hand out work", but it is a stand-in, not a real flag.
4. **A decision on the three arguable calls above**, especially F9.
