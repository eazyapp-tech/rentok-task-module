---
title: "Session handoff: the repo made readable for everyone (2026-09-05)"
date: 2026-09-05
owner: "Sanchay"
status: "current. A new session reads this first"
session: "Claude Code, cwd rentok-backend, session 9e6378dd-7ae1-4f04-9164-ca815bb228c8"
tags: [rentok, tasks, handoff, session]
---

# Session handoff, 5 September 2026

## What is in here

Where the Task module redesign work stands after the repo was restructured for every reader in the company, what moved where, what was reconciled against the 5 August decisions, what is still open, and what the next session does first. For a new Claude session, and for anyone who had a link into the old layout. The product thinking itself is unchanged: D1 to D85 and F1 to F59 stand as they were.

## Contents

- [1. Why the repo changed](#1-why-the-repo-changed)
- [2. Old path, new path](#2-old-path-new-path)
- [3. What was reconciled, and where](#3-what-was-reconciled-and-where)
- [4. What is open](#4-what-is-open)
- [5. What the next session does first](#5-what-the-next-session-does-first)
- [6. Traps carried forward](#6-traps-carried-forward)
- [7. Where truth lives](#7-where-truth-lives)

## 1. Why the repo changed

Jatin and Vivek (engineering), Ishika and Nitish (design), Srijan, Nimit and Anil (business, CTO, marketing) each said the repo was too hard to read, understand, navigate and track to start from. Sanchay ruled it is for everyone in the company. The repo had grown as the product process's working notebook: session handoffs, review logs, a changelog that argues, version-history blocks, and nothing had been folded down. Its front door pointed at a session handoff written for Claude; its stage tables had gone stale on 5 August; the spec cited decisions by number and refused to restate them; a second product (the home-screen feed) sat beside the first with the same weight.

The shape now: a README that is a navigation surface by role; one plain-language feature map that everyone reads first (00, written after Sanchay rules on the sorted inventory); the brief, the requirements, the build sequence, the spec and the engineering asks as 01 to 05; the decision log with an index; evidence under `reference/`; history under `history/`.

## 2. Old path, new path

Any link pasted before 5 September into Slack, Linear or a PR points at the left column. GitHub does not redirect renamed files.

| Was | Is now |
|---|---|
| `README.md` | `README.md` (rewritten) |
| (did not exist) | `00-feature-list.md` (new: every item, one line, by stage; generated from 02) |
| (did not exist) | `00-feature-map.md` (new, after the content gate) |
| `Task Module Brief.md` | `01-brief.md` |
| `feature-requirements.md` | `02-requirements.md` (its five version-history blocks moved to `history/02-requirements-version-history.md`) |
| `build-sequence.md` | `03-build-sequence.md` |
| `spec-stage-1-2.md` | `04-spec-stages-1-2.md` |
| `engineering-handoff.md` | `05-engineering-asks.md` |
| `CHANGELOG.md` | `CHANGELOG.md` (a "Find a decision" index at the top; three annotations and two history lines added) |
| `Task Module - Feature Gap Audit.md` | `reference/feature-gap-audit.md` |
| `grounding-notes.md` | `reference/grounding-notes.md` |
| `pending-tasks/` (14 files) | `reference/pending-tasks/` |
| `HANDOFF.md` | `history/2026-08-04-session-handoff.md` (historical) |
| `review-findings.md` | `history/2026-07-21-review-round-1.md` |
| `review-round-2-decisions.md` | `history/2026-08-03-review-round-2.md` |
| `archive/` (3 files) | `history/archive/` (only their dead links fixed) |
| `references/` (empty) | removed |

Every relative link and anchor in the repo was rewritten and checked by script: zero missing targets apart from the two files not yet written when the check ran (the map and this note).

## 3. What was reconciled, and where

These were wrong or stale on 4 September and are right now. Each change is marked in place with the date.

| Finding | Fix | Where |
|---|---|---|
| The stage tables in the build sequence and the engineering asks missed M5 and M6, both decided with D84 on 5 Aug | Added to both, with the one-line reason; the spec's stage headings are now the single source and a script checks the three files agree | 03 "Ships:" lines; 05 section 1 |
| The engineering asks still asked three questions Sanchay had answered (F44's behaviour, F58 is a new build, the D69 proxy flags) and carried a wrong F29 note | Moved to a "Settled since the first handoff" table; ask 3 rewritten to what is still asked; F29 note corrected (a rating is its own type with a scale) | 05 sections 3, 5 |
| The spec cited D44 for F37's edit log and D38 for F38's archive; neither decision says that | Citations removed with a dated note; F37 stands as its own requirement; F38 cites D57's archive flag | 04, F37 and F38 |
| CHANGELOG entries D7, D36, D50 were killed by D64 but carried no annotation | The same one-line annotation the other superseded entries have, dated | CHANGELOG |
| CHANGELOG's own history said D85 is "property, not account" (the reversed ruling) and that D42 had been annotated (it had not) | Both lines corrected and dated | CHANGELOG, "Changelog of this changelog" |
| README said D1 to D83 and requirements v2.3; the files say D85 and v2.4 | README rewritten; numbers taken from the files | README |
| Bare decision labels in the spec forced 49 jumps to the changelog | Each cited label now carries its meaning where the sentence did not already | 04 |
| Nothing in the repo addressed a designer | A by-role reading table and a "What a designer draws in stage 2" table, one row per item, from the spec's own words | 04, after "How to read it" |
| Nothing in the repo addressed marketing, business, or a newcomer | The feature map (00), with "How we talk about it" and "What success looks like" | 00, after the gate |
| The pending-tasks pack sat at root with the same weight as the redesign | Fenced under `reference/`; kept in this repo because stages 6 and 7 build on it (F58, M3, D66) | reference/pending-tasks/ |
| The map's first draft inverted the message model (said one link per day; the morning message carries all links, the nudge carries one), said every task has a due time (a date-only case exists), and called S2L "our biggest customer" (no source says so). Caught by the doc-handoff-review fact-check pass before handoff. A second round found three more: the no-stage count (fourteen, not thirteen), branching counted as a thirteenth question type, and one-each versus fan-out | All corrected; the pass's other findings (thirteen items in no stage now said in the map, the Foundation bucket defined, every "Protects Fn" cell now names the thing, surfaces and names in the designer table, Linear caveat in README and 05) applied | 00, 04, 05, README |
| Locked sentence 10 said "fan-out"; every product doc said "one-each" | Sanchay ruled one-each; sentence 10 changed and annotated; older entries keep their word | CHANGELOG, 00, 02, 05 |
| `rentok-checklist-library` README still described "two parallel workstreams" and carried live-looking copies of the brief and audit | Its Workstream B section now points here; the two copies carry a "frozen copy" line naming the new paths. Uncommitted on that repo's branch `docs/task-module-redesign-brief` | that repo |

## 4. What is open

Carried from the 4 August handoff's "open, non-blocking" list and from this session.

- **The feature map (00)** is written only after Sanchay rules on `history/feature-map-inventory.md` section 10 (seven calls). Until then README, 02 and 04 link to a file that does not exist.
- **Linear.** Project "Task Module redesign" under team RentOk: seven stage issues carrying the estimate, 22 sub-issues for stages 1 and 2, one issue for the D69 practice question. Created only after the docs are approved. Then 05 and the README stage table get their links.
- **Commit and push.** Nothing is committed. When Sanchay says so: two commits on `chore/readable-for-everyone`, the pure moves first, then the content edits. The checklist-library changes are a third, separate commit on that repo.
- **Vault mirror** `RentOk/PRDs/Task Module/`: rename to match the repo inside Obsidian through the obsidian skill, so its wikilinks update; mirror 00 there. After the push.
- **Whether F58 (alert into work) moves earlier than stage 7.** Nothing technical holds it there and it is the most demo-able feature in the set. Sanchay's call when the stages are priced.
- **Sections change the shape of the checklist structure** (M6). Small, but the builder, the runner, the report and the validator all read that column; engineering names every reader before the migration (05 section 6, item 5).
- **Voice notes** are listed under both F29 and F15b and should be merged into F15b. Not done; it changes a signed requirements doc and waits for Sanchay's word.
- **Whether F35's "conditional items" is already covered by the branching D84 committed.** The map's parked table says the scanning half is parked and asks Sanchay how much of the conditions half remains.
- **F10's floor choice:** one floor today; whether the picker says so or accepts several is open before design starts (the designer table in 04 flags it).
- **The brief rounds the repeat-complaint figures to 32% and 33%; the map and D83 say 31.5% and 33.4%.** One precision, Sanchay's call; the brief is signed.
- **The em-dash sweep** across the untouched prose, if Sanchay wants it. Its own diff.

## 5. What the next session does first

1. Read this file, then the README.
2. If the map is not yet written: read `history/feature-map-inventory.md` and its section 11 rulings, then write 00 once, under the feature-map skill, and run the bar file `~/agent-config/bars/feature-map.md`.
3. Run the checks: `linkcheck.py` over the repo (zero missing), the stage-agreement script, the bare-label sweep on README, 00, 04, 05, and `doc-handoff-review` on 00, README, 04, 05.
4. Only then Linear, the push, the vault.

## 6. Traps carried forward

From the 4 August handoff, still true, plus two from this session.

1. Announcing an edit in a decision and never making it. The review gate caught it twice in August; the stage tables missing M5 and M6 were the same failure in a different place.
2. Overstating a source in the direction that helps the argument.
3. Writing code claims from docs instead of from `rentok-backend`.
4. **New:** a "source of truth" that carries its own history line can contradict itself. The CHANGELOG said two opposite things about D85 for a month. Check every restatement of a ruling, not only the entry.
5. **New:** a doc reconciled once does not stay reconciled. Three docs went stale in the same week their source changed. The stage-agreement script exists so the next drift is caught by a check, not a reader.

## 7. Where truth lives

| What | Where |
|---|---|
| Decisions and the twelve sentences | `CHANGELOG.md`; its index lists every number in order |
| Requirements and their cut order | `02-requirements.md` |
| Stage contents | `04-spec-stages-1-2.md` headings for stages 1 and 2; `03-build-sequence.md` for 3 to 7 |
| Price and build status | Linear, project "Task Module redesign", once created |
| What the code does today | `04` per item, `reference/grounding-notes.md`, `reference/feature-gap-audit.md` |
| The home-screen feed | `reference/pending-tasks/` |
| Session state | this file; the memory note `project_task_module_redesign.md` points here |
