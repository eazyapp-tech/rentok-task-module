# RentOk Task Module

The redesign of RentOk's Task module: from a checklist runner into the tool a property uses to run its own work. It tells people what to do, proves it was done, and lets each level see and help. These are the product documents for that redesign, written for everyone at RentOk: leadership, business, marketing, sales, support, design, QA, engineering, product. The map and the brief are plain language; the spec and the engineering asks carry the technical detail. **Docs only. No code ships from here.**

## Contents

- [Start here, by role](#start-here-by-role)
- [The reading path](#the-reading-path)
- [The stages](#the-stages)
- [Ship gates](#ship-gates)
- [The rules every doc follows](#the-rules-every-doc-follows)
- [Where design work goes](#where-design-work-goes)
- [Related](#related)

## Start here, by role

| You are | Open this | Then |
|---|---|---|
| **New to this, any role** | [00-feature-list.md](00-feature-list.md), then [00-feature-map.md](00-feature-map.md) | Three minutes for the list. Ten for the map: what we build, for whom, moment by moment, what is deferred, what is not built. |
| **Leadership, business** (Srijan) | [00-feature-map.md](00-feature-map.md) | Its "What success looks like" and "How it arrives" sections. Price and status: [Linear](https://linear.app/rentok/project/task-module-redesign-4bafc030f838), and nowhere else. |
| **Marketing, sales** (Anil) | [00-feature-map.md](00-feature-map.md) | Its "How we talk about it": the sentences to use and the words never to use. Then [the stages](#the-stages) for what can be promised when. |
| **Support** | [00-feature-map.md](00-feature-map.md) | The moments, then the vocabulary in [the rules](#the-rules-every-doc-follows). |
| **Design** (Ishika, Nitish) | [00-feature-map.md](00-feature-map.md), then the brief's [who we are building for](01-brief.md#who-we-are-building-for--the-chain-of-accountability) and [the bet](01-brief.md#the-bet-accountability-without-surveillance) | [What a designer draws in stage 2](04-spec-stages-1-2.md#what-a-designer-draws-in-stage-2): each item with its surface, in the spec's own words, plus the four stage-1 items needing screens. |
| **QA** (Devendra) | [04-spec-stages-1-2.md](04-spec-stages-1-2.md) | Each item's numbered Acceptance and its Edges; the [by-role table](04-spec-stages-1-2.md#how-to-read-it-by-role) says what to skip. |
| **Engineering, pricing the stages** (Jatin) | [00-feature-list.md](00-feature-list.md) for the shape, then [05-engineering-asks.md](05-engineering-asks.md) | Every item named with its spec section, then [the dependency map](03-build-sequence.md#the-dependency-map). Estimates: the stage's Linear issue, linked from every row. |
| **Engineering, building stage 1 or 2** (Jatin, Vivek) | [05-engineering-asks.md](05-engineering-asks.md#stages-1-and-2-item-by-item) | The item's row links its spec section: what it is, today, acceptance, data and API, edges. |
| **CTO** (Nimit) | [05-engineering-asks.md](05-engineering-asks.md) | The trigger nobody owns, the staff access default, what is still engineering's to decide, and the [four live defects](05-engineering-asks.md#8-four-live-defects-for-nimit). Then price the stages with Jatin. |
| **Product** (Sanchay) | [CHANGELOG.md](CHANGELOG.md#find-a-decision) | The decision index, then [02-requirements.md](02-requirements.md). |
| **A new Claude session** | [history/2026-09-05-restructure.md](history/2026-09-05-restructure.md) | The current handoff: where the work stands, what moved where, what is open. |

## The reading path

In order. Each file opens with what it is and what it deliberately is not.

| File | What it is | What it is not |
|---|---|---|
| [00-feature-list.md](00-feature-list.md) | Every feature on one page, one line each: by stage, then the fourteen in no stage yet, then parked, then not building. For scanning. | Reasons, arguments, or detail. |
| [00-feature-map.md](00-feature-map.md) | The whole product in plain words, as the property's life: spine, supporting, parked with what revives it, not built and why. | A build order, an estimate, or a spec. |
| [01-brief.md](01-brief.md) | The why: the problem, who we build for, the bet (accountability without surveillance), the moat, what success looks like. | A list of features. |
| [02-requirements.md](02-requirements.md) | Every requirement, one line each, F1 to F59, in the order they should survive a cut: bands A, B, C, Later. With migrations and prerequisites. | A build order. |
| [03-build-sequence.md](03-build-sequence.md) | Seven stages, each a real ship point, and the dependency map. No estimates by design. | A commitment on dates. |
| [04-spec-stages-1-2.md](04-spec-stages-1-2.md) | The build spec for stages 1 and 2: per item, what it is, what the code does today, acceptance, data and API, edges. | A spec for stages 3 to 7. |
| [05-engineering-asks.md](05-engineering-asks.md) | The asks: price the stages, find what fires repeating work today, check the staff access default. What is settled, what is still engineering's. | A spec. |
| [CHANGELOG.md](CHANGELOG.md) | D1 to D85, each with the alternative rejected, and the twelve locked sentences. **On any conflict this file wins.** [Find a decision](CHANGELOG.md#find-a-decision) lists every number. | Reading material for a newcomer; start at 00. |

Evidence and history sit off the path: [reference/](reference/) holds the code-level gap audit, the grounding notes and the pending-tasks pack; [history/](history/) holds the two review rounds, the session handoffs, the version history and the archive of superseded docs.

## The stages

Price and status live in Linear, project [Task Module redesign](https://linear.app/rentok/project/task-module-redesign-4bafc030f838), and nowhere else. This table only says what each stage is and where to read it.

| Stage | What it does | What a manager can do after it | Read |
|---|---|---|---|
| 1 | Make what already exists safe: permissions, validation, an edit log, expiry. Invisible on purpose. | Nothing new. The record can be trusted. | [spec](04-spec-stages-1-2.md#3-stage-1--make-what-already-exists-safe) · [pricing rows](05-engineering-asks.md#stages-1-and-2-item-by-item) · [Linear](https://linear.app/rentok/issue/REN-661) |
| 2 | Give the work an owner, a real cadence, and something worth filling in | Scope to rooms, assign pooled or one-each, set a cadence, create a one-off, build a real checklist, start from a library in Hindi. | [spec](04-spec-stages-1-2.md#4-stage-2--give-the-work-an-owner-a-real-cadence-and-something-worth-filling-in) · [pricing rows](05-engineering-asks.md#stages-1-and-2-item-by-item) · [Linear](https://linear.app/rentok/issue/REN-662) |
| 3 | Make it chase itself | Due dates, reminders and escalation; skip or reschedule one occurrence; reassign; "couldn't do it"; complete on someone's behalf; a leaver's work comes back. | [build sequence](03-build-sequence.md#stage-3--make-it-chase-itself) · [Linear](https://linear.app/rentok/issue/REN-663) |
| 4 | The proof | Do the task in the runner with proof; partial work survives a bad connection; photos compressed; each person sees their own record. | [build sequence](03-build-sequence.md#stage-4--the-proof) · [Linear](https://linear.app/rentok/issue/REN-664) |
| 5 | The fault loop | An item carries a problem; a problem raises a pre-filled complaint with one tap; the open ones for that room show first. | [build sequence](03-build-sequence.md#stage-5--the-fault-loop) · [Linear](https://linear.app/rentok/issue/REN-665) |
| 6 | Letting each level see | Review; one list across all three sources; history per room or tenant; the first insight cut; the exception view. | [build sequence](03-build-sequence.md#stage-6--letting-each-level-see) · [Linear](https://linear.app/rentok/issue/REN-666) |
| 7 | The on-ramp and the things that run themselves | Manage routines; see a routine's reach before switching it on; self-pausing routines; my own tasks; a move-out creates the prep task; an alert becomes work. | [build sequence](03-build-sequence.md#stage-7--the-on-ramp-and-the-things-that-run-themselves) · [Linear](https://linear.app/rentok/issue/REN-667) |

## Ship gates

- The staff task page opens in under 3 seconds on a 2G connection the first time, with nothing saved on the phone, and half-done work survives a closed tab, a dropped signal and a phone restart.
- RentOk's starter templates ship in Hindi as well as English (D76, translation of the app itself is deferred). The manager writes her tasks and her own checklists in her own script; the app's own words (Submit, Overdue, Approve) stay English.

## The rules every doc follows

- **Vocabulary:** checklist · task · schedule · staff / manager / owner · beds. Never SOP, landlord, units. Indian-English is fine.
- **A label carries its meaning.** D26 or F12 never appears bare in prose; the words beside it say what it rules or requires. The label is for finding more, not the only carrier of meaning.
- **CHANGELOG wins.** If any doc contradicts a decision there, the doc is stale.
- **History is kept and marked, never deleted.** Superseded entries carry an annotation; moved files carry a note saying where they went.
- **One place for live state.** Price and build status: Linear. Decisions: CHANGELOG. Requirements: 02. Nothing is copied into a second place to be read later.
- **No code in the map or the brief.** File paths, flags and columns live in the spec, the engineering asks and the reference folder.
- **Plain words, no em dashes** in anything written from 5 September 2026 on. Older prose is left as it was.

## Where design work goes

No screens exist yet; design takes the stage 2 spec as input. When designs exist they live in `design/`, one file per stage-2 item, named after the item, with the Figma link on the file's first line.

## Related

- [reference/pending-tasks/](reference/pending-tasks/README.md): the manager home-screen needs-attention feed. **Not the Task module.** It is here because stages 6 and 7 build on it (an alert becomes work; system-raised tasks share the categories). Its full build-out is parked at backend issue [#6249](https://github.com/eazyapp-tech/rentok-backend/issues/6249).
- [rentok-checklist-library](https://github.com/eazyapp-tech/rentok-checklist-library): the starter-template content and its recommendation design. One requirement inside this redesign (F9), not a parallel workstream.
- [rentok-backend](https://github.com/eazyapp-tech/rentok-backend): the code. Issue [#6363](https://github.com/eazyapp-tech/rentok-backend/issues/6363) tracks finding and authenticating the scheduler's caller (P1).
- Vault mirror: `RentOk/PRDs/Task Module/` in Obsidian, synced from this repo. This repo wins.
