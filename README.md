# RentOk Task Module — Redesign Docs

Product docs for turning RentOk's Task module from a checklist runner into the tool a property uses to run its own standards: it tells people what to do, proves it was done, and lets each level see and help.

**Design docs only — no code ships from here.**

## Start here

| Read | What it is |
|---|---|
| **[CHANGELOG.md](CHANGELOG.md)** | **The source of truth.** 12 canonical sentences (the exact words to use) + numbered decisions D1–D18, each with the alternative rejected. If any doc contradicts this file, this file is right. |
| [feature-requirements.md](feature-requirements.md) | Every requirement, one line — the canonical **F1–F40** numbering + the v2 backlog. |
| [Task Module Brief.md](Task%20Module%20Brief.md) | The WHY: the problem, the personas, the bet, the mission. |
| [Task Module - Feature Gap Audit.md](Task%20Module%20-%20Feature%20Gap%20Audit.md) | Engineer evidence: 15 domains, ~250 capabilities scored against the code as it is today. |
| [review-findings.md](review-findings.md) | The 2026-07-21 product-lens review — ~30 findings + 7 strategic calls awaiting a decision. |
| [grounding-notes.md](grounding-notes.md) | What the code actually does today, from this cycle's grounding sweeps. |

## The model, in one breath

Nothing acts on its own — the system suggests, a person confirms. A task tied to a real thing (a due, a tenant, a room, an asset) **reads** that thing's state and suggests; it never writes into it. Entity links give context, filtering, navigation, and history — not completion. A task and a complaint raised from it are linked but run on independent statuses. Three sources of task (system-raised, person-assigned, self-kept) appear in one category-filtered list. The proof belongs to the person who collected it — no fines, no scorecard.

Full wording in [CHANGELOG.md](CHANGELOG.md). Use those sentences exactly.

## Scope

**The Task module redesign is the whole scope.** The **checklist template library** is one requirement inside it (**F9** — it solves setup, so a manager picks ready templates instead of facing a blank box). It has its own detailed docs and template content in the [`rentok-checklist-library`](https://github.com/eazyapp-tech/rentok-checklist-library) repo, but it ships as part of this redesign — not as a parallel workstream.

## Status

**In progress.** The model and the requirements are locked; the spec layer is not yet written.

- ✅ CHANGELOG (D1–D18), feature requirements (F1–F40), Brief, Feature Gap Audit, grounding notes, review findings
- ⏳ To write: a short vision brief, personas, journeys, the PRD (rebuilt on F1–F40), per-flow workflow specs, a new pre-mortem
- ⛔ **`archive/` holds superseded docs — do not build from them.** The v0 brief, and a PRD and pre-mortem written on the old (wrong) "write-backs" model. Each carries a header explaining what is wrong and what to read instead. The old PRD's F-numbers collide with the canonical list — never cite an F-number from `archive/`.
- 🔓 **Open:** 7 strategic calls in [review-findings.md](review-findings.md) need a decision before the spec layer is written.

## Ship gates (hard)

- The staff runner cold-loads in under 3 seconds on a 2G connection with no stored files, and partial work survives a tab close, a network drop, and a phone restart.
- The runner ships in Hindi plus at least one regional language at launch.

## Vocabulary (locked)

checklist · task · schedule · staff / manager / owner · beds. Never SOP, landlord, units, seamless, leverage. Indian-English is fine.

## Related

- [`rentok-checklist-library`](https://github.com/eazyapp-tech/rentok-checklist-library) — the F9 sub-project's docs and template content
- [`rentok-backend`](https://github.com/eazyapp-tech/rentok-backend) — the codebase; issue **#6249** tracks the deferred full pending-tasks registry build-out
- Vault mirror: `RentOk/PRDs/Task Module/`
