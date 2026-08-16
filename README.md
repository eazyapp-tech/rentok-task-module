# RentOk Task Module — Redesign Docs

Product docs for turning RentOk's Task module from a checklist runner into the tool a property uses to run its own work: it tells people what to do, proves it was done, and lets each level see and help.

**Design docs only — no code ships from here.**

## Start here

| Read | What it is |
|---|---|
| **[HANDOFF.md](HANDOFF.md)** | **Read this first in a new session.** Where the work stands, what is open, what to do next, and the mistakes this project has already made twice. |
| **[CHANGELOG.md](CHANGELOG.md)** | **The source of truth.** 12 canonical sentences (the exact words to use) + numbered decisions **D1–D83**, each with the alternative rejected. If any doc contradicts this file, this file is right. |
| [feature-requirements.md](feature-requirements.md) | Every requirement, one line — the canonical **F1–F59** in bands A / B / C / Later. v2.3. |
| [build-sequence.md](build-sequence.md) | **7 stages**, each a real ship point, with the dependency map. No estimates by design. |
| [spec-stage-1-2.md](spec-stage-1-2.md) | **The build spec for stages 1 and 2.** Acceptance criteria per requirement, data and API shapes, edge cases, migration order, and the defects found while grounding it. |
| [engineering-handoff.md](engineering-handoff.md) | The three asks for Nimit and Jatin. |
| [Task Module Brief.md](Task%20Module%20Brief.md) | The WHY: the problem, the personas, the bet, the moat. |
| [Task Module - Feature Gap Audit.md](Task%20Module%20-%20Feature%20Gap%20Audit.md) | Engineer evidence: 15 domains scored against the code as it is today. Its `[TS]` tier is scored against work/inspection tools, not PG software — see the note at its top. |
| [grounding-notes.md](grounding-notes.md) | What the code actually does today. |
| [review-round-2-decisions.md](review-round-2-decisions.md) | The argument behind D67–D79. **Historical** — its own D-numbers are off by three; use the mapping at its top. |
| [review-findings.md](review-findings.md) | The 2026-07-21 round-1 review. **Historical** — strategic call #1 is withdrawn (D74). |

## The model, in one breath

Nothing acts on its own — a person decides. A task tied to a real thing (a due, a tenant, a room, an asset) **shows that thing's live state**; it never writes into it, and it never judges whether the work is done. Entity links give context, filtering, navigation, and history — not completion. A task and a complaint raised from it are linked but run on independent statuses. Three sources of task (system-raised, person-assigned, self-kept) appear in one category-filtered list. An alert can be turned into work — one task per item, with an owner and a record. The proof belongs to the person who collected it — no fines, no scorecard.

Full wording in [CHANGELOG.md](CHANGELOG.md). Use those sentences exactly.

## What we are actually betting on

- **The problem (D82):** the work fails three ordinary ways — someone forgets, someone does it late, someone says it is done when it is not — and **nobody finds out until it has become a complaint or a vacancy.**
- **The moat (D80, widened by D81):** *a property runs on a system, not on one person's memory* — so it survives the manager's absence and the staff churning. The surface is the property's **whole** work across every role, not just its cleaning routines.
- **Measured (D83):** 31.5% of room-linked complaints are a repeat on the same room within 7 days. The vacancy half of the cost chain was tested and **cut** — most empty rooms are a demand problem, not a readiness one.

## Scope

**The Task module redesign is the whole scope.** The **checklist template library** is one requirement inside it (**F9** — it solves setup, so a manager picks ready templates instead of facing a blank box). It has its own detailed docs and template content in the [`rentok-checklist-library`](https://github.com/eazyapp-tech/rentok-checklist-library) repo, but it ships as part of this redesign — not as a parallel workstream.

## Build sequence

Seven stages, each a point you could stop at and still have something coherent. Full ships-list and dependency map in [build-sequence.md](build-sequence.md).

| Stage | What it does |
|---|---|
| 1 | Make what already exists safe — permissions, validation, audit, expiry. Invisible on purpose. |
| 2 | Give the work an owner, a real cadence, and something worth filling in |
| 3 | Make it chase itself |
| 4 | The proof |
| 5 | The fault loop |
| 6 | Letting each level see |
| 7 | The on-ramp and the things that run themselves |

## Status

**Blocked on engineering, not on product.** The model, the requirements and the sequence are locked. Nobody has priced anything, and every remaining product question either depends on that price or on putting the thing in front of a real user.

- ✅ CHANGELOG (D1–D83), feature requirements (F1–F59), build sequence, engineering handoff, Brief, Feature Gap Audit, grounding notes, two adversarial review gates
- ✅ **[spec-stage-1-2.md](spec-stage-1-2.md)** — the build spec for stages 1 and 2, grounded against the code on 2026-08-05.
- ⏳ **Next:** design (screens and states) and QA (test scenarios). Neither exists yet; both take the spec as input.
- ⏸ **Waiting on Nimit and Jatin:** price the seven stages · confirm P1 · confirm D69's permission proxy. See [engineering-handoff.md](engineering-handoff.md).
- ⛔ **`archive/` holds superseded docs — do not build from them.** The v0 brief, and a PRD and pre-mortem written on the old (wrong) "write-backs" model. Each carries a header explaining what is wrong and what to read instead. The old PRD's F-numbers collide with the canonical list — never cite an F-number from `archive/`.

**Deliberately not being written:** a PRD covering all 43 Band A+B requirements (it would be rewritten the moment stage 2 meets real managers), specs for stages 3–7, and a new pre-mortem before the scope is cut. Vision brief, personas and journeys are covered by the Brief or block nothing — they are not debt.

## Ship gates (hard)

- The staff runner cold-loads in under 3 seconds on a 2G connection with no stored files, and partial work survives a tab close, a network drop, and a phone restart.
- RentOk's starter templates ship in **Hindi as well as English** (D76). App-level translation is deferred — the manager writes her tasks and her own checklists in her own script, and the app's own words (Submit, Overdue, Approve) stay English.

## Vocabulary (locked)

checklist · task · schedule · staff / manager / owner · beds. Never SOP, landlord, units, seamless, leverage. Indian-English is fine.

## Related

- [`pending-tasks/`](pending-tasks/) — home-screen needs-attention feed (`getPendingTasks`). Home is a stack of one; View All is the hub. Not the checklist / task / schedule work in this repo.
- [`rentok-checklist-library`](https://github.com/eazyapp-tech/rentok-checklist-library) — the F9 sub-project's docs and template content
- [`rentok-backend`](https://github.com/eazyapp-tech/rentok-backend) — the codebase. Issue **#6249** tracks the deferred full pending-tasks registry build-out; issue **#6363** tracks finding and authenticating the existing scheduler (P1).
- Vault mirror: `RentOk/PRDs/Task Module/` — currently behind.
