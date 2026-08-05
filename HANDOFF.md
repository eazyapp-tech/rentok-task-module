---
title: "Task Module — Session Handoff"
date: 2026-08-04
owner: "Sanchay"
status: "current — read this first in a new session"
tags: [rentok, tasks, handoff, session]
---

# Session Handoff — 2026-08-04

Read this before anything else in a new session. It says what is decided, what is open, what to do
next, and which mistakes this project has already made twice.

---

## 1. Where the work is

**The product thinking is done.** Brief → requirements → build sequence → engineering handoff, with two
adversarial review gates and four competitor classes behind it. **83 decisions (D1–D83), 59 requirements
(F1–F59), 7 build stages.**

**It is now blocked on engineering**, genuinely. Nobody has priced anything, and every remaining product
question either depends on that price or on putting the thing in front of a real user.

**Nothing has been built. No code has been written. This repo is docs only.**

---

## 2. What changed today (13 commits, `7d28ede`…`8ca9e51`)

| Theme | What happened |
|---|---|
| **Round-2 adversarial review** | 24 findings worked one at a time → **D67–D79**. Band rule added (D67): *a thing Band B would lie without is Band B*. Five items moved C→B. |
| **Two locked decisions corrected** | **D13** would have shipped every permission open to everyone, making D55 false in production — staff now default to "see only my own" (**D69**). **D22** promised the manager a first look that nothing implemented — she now sees the same exception list as the owner (**D71**). |
| **The moat, named** | **D80** — it is canonical sentence 8: *a property runs on a system, not one person's memory*. Two competitor-shaped answers were tried and discarded first. |
| **The story, widened** | **D81** — the module is the property's whole work, not its routines. Six roles, ~17 job types at one account (S2L). |
| **The problem, restated** | **D82** — the work fails three ways (forgotten / late / said-done), and **nobody finds out until it becomes a complaint or a vacancy**. |
| **The cost chain, measured** | **D83** — 31.5% of room-linked complaints are a repeat on the same room within 7 days. The vacancy half was tested and **cut** — most empty rooms are a demand problem, not a readiness one. |
| **Sequencing** | `build-sequence.md` (new) — 7 stages, each a real ship point. F9/F13/F29/F30 moved to stage 2; F51 moved to Band B and stage 3. |
| **Handoff to engineering** | `engineering-handoff.md` (new) — three asks: price the stages, confirm P1, confirm D69's proxy. |

---

## 3. What is open

### Blocking — none of it is product's

1. **Engineering must price the seven stages.** Nimit and Jatin. Rough is fine. Everything waits on this.
2. **P1 — the cron exists but is not in this repo.** Sanchay confirmed it runs (the product works today); it
   simply is not registered anywhere in `rentok-backend`. **The job is to find it and authenticate it**, not
   to build one. Backend issue **#6363**. *(Earlier drafts wrongly said no scheduler exists — corrected.)*
3. ~~**F36 must not ship before the question-type list is settled.**~~ **Settled 2026-08-05 — D84.** The
   union is now fixed and F36 validates against it. **M5** (rename `rating_5` / `rating_10` / `dropdown`)
   runs before F36 and handles the day-one rejection risk. **That risk was understated: it is 48.2% of
   checklists, not 12.7%** — the old figure used questions as the denominator. Re-verified 5 Aug 2026, which
   also confirmed there are **no unknown type values** beyond the three, so M5's mapping is complete.

### Answered by Sanchay, already in the docs

- **F44** → a checklist with open tasks cannot be edited; **save as a new copy** instead.
- **D69's permission proxy** → confirmed. `view_team` / `add_team` / `edit_team`.
- **F58** → the pending-tasks feed has **no assign action today**. It is a new build.
- **M3** → adopt the alerts' existing categories for tasks, **and let operators add their own custom
  categories** and filter by them. One guard, from D21: suggest previously-used categories as she types, or
  three managers create "Cleaning", "cleaning" and "Housekeeping" and the filter rots. **Open sub-question:
  are custom categories per property or per account?** Per property breaks F22's cross-property filter.
- **Voice note** → keep as product intent, push for it, likely V1.1. It is listed twice (F29's voice item
  and F15b) — **merge them**.

### Question types — committed 2026-08-05 (D84)

**Sanchay's call: build them all.** The deferral recorded below is reversed; the union is settled and F36
validates against it. Two migrations follow — **M4** (`structure` gains sections and branching) and **M5**
(rename the live `rating_5`/`rating_10`/`dropdown` values before F36 can reject unknown types). Full list
and consequences in **D84**; the build lands in stage 2 (F29/F30).

The evidence behind the list, tested against 2,698 live questions:

- **Free or nearly** — make `number` findable (152 questions type "how many…" while the box sits unused),
  pass/fail as a three-option dropdown, and add `rating`/`dropdown` to the backend's type list so they stop
  being invisible.
- **Real builds, evidence-backed** — branching (58 questions fake it with *"if yes, explain"*), number with
  a unit (49), date (17), time, sections (59 templates have 10+ questions).
- **Judgement, not evidence** — multi-select and several-photos. Demand for these is **invisible by
  construction**: someone wanting "tick all that are broken" writes five yes/no questions, which looks like
  ordinary use. Include them; they are cheap and nothing downstream changes.
- **Dropped** — **grid.** The only type whose answer is a table rather than a value, which changes history,
  insights, export and validation. Revisit only if it turns out to be the answer to the property-wide audit
  (D70).

~~**The PM call: do not commit the real builds yet.**~~ **Reversed 2026-08-05 (D84).** The argument was that
two weeks of ~10 pilot properties would teach more than any available query. It was outweighed by F36: with
the union unsettled, stage 1's validation has no stable target, and a permissive union is validation that
validates nothing.

### Open, non-blocking

- Whether **F58** moves earlier than stage 7 — nothing technical holds it there, and it is the most
  demo-able single feature in the set.
- **Sections** change the shape of `structure` (a flat array today). Small, but the builder, runner, report
  and F36 all read that column.

---

## 4. The plan for the next session

**Agreed order:**

1. ~~**Fix `README.md`**~~ — **done 2026-08-04.** Rewritten against D83 / F59 / the 7 stages: the "suggests"
   wording is gone (D65), the Hindi ship gate now says what D76 actually decided, the settled strategic calls
   are out, and `build-sequence.md`, `engineering-handoff.md` and this file are linked. The same pass fixed
   `build-sequence.md`'s dependency map, which still claimed *"there is no scheduler in the repo."*
2. ~~**Write the spec for stages 1 and 2 only**~~ — **done 2026-08-05**, at
   [spec-stage-1-2.md](spec-stage-1-2.md). Grounded against `rentok-backend` file-by-file, not from these
   docs. It added **M4** and **M5** (D84), found **three live defects** (§6), and left **six questions for
   engineering** plus one still product's — per-property or per-account custom categories, which blocks F32.
3. *(pause for engineering's estimates)*
4. **Pre-mortem, scoped to what actually ships** — not before. A pre-mortem is a pre-ship artifact; running
   it against an uncut scope means running it twice. The archived one is superseded anyway.
5. **Sync the vault mirror** at `RentOk/PRDs/Task Module/` — 13 commits behind.

**Deliberately not doing:** a PRD for all 43 Band A+B requirements (it would be rewritten the moment stage 2
meets real managers), specs for stages 3–7, and the *"vision brief, personas, journeys"* the README lists as
to-write — the Brief covers personas and the bet, and journeys block nothing. **Delete them from the list
rather than carry them as debt.**

**After the stage 1+2 spec, the next dependencies are design (screens and states) and QA (test scenarios).**
Nothing exists for either.

---

## 5. Where truth lives

| File | What it is | Trust |
|---|---|---|
| **CHANGELOG.md** | **Source of truth.** 12 canonical sentences + D1–D83, each with the rejected alternative. If a doc contradicts it, the doc is stale. | Current |
| feature-requirements.md | F1–F59 in bands A / B / C / Later. v2.3. | Current |
| build-sequence.md | 7 stages, dependency map, break points. **No estimates by design.** | Current |
| spec-stage-1-2.md | The build spec for stages 1 and 2. Acceptance criteria, data and API shapes, edges, migration order. **§1 is the only code-verified picture of the module as it stands** — trust it over grounding-notes where they differ. | Current |
| engineering-handoff.md | The three asks for Nimit and Jatin. | Current |
| Task Module Brief.md | The WHY, the bet, the moat. | Current |
| Task Module - Feature Gap Audit.md | Code-level evidence, 15 domains. **Its `[TS]` tier is scored against work/inspection tools, not PG software** — see the note at the top. | Current, scoped |
| grounding-notes.md | What the code does today. | Current |
| review-round-2-decisions.md | The argument behind D67–D79, including the three places the review was wrong. **Its own D-numbers are off by three — use the mapping at the top.** | Historical |
| review-findings.md | Round-1 review. **Marked historical**; strategic call #1 is withdrawn (D74). | Historical |
| README.md | The front door. Rewritten 2026-08-04 against D83 / F59 / the 7 stages. | Current |
| `archive/` | Superseded PRD, pre-mortem, v0 brief. **Never build from these; their F-numbers collide with the live list.** | ⛔ |

---

## 6. Traps — mistakes this project has made, some twice

Read these. Two of them recurred *within* today.

1. **Announcing an edit and not making it.** D71 said *"D22 is therefore reworded"* — D22 was untouched.
   D70 said *"D60's wording is corrected"* — untouched. Both caught by the review gate. **If a decision says
   it corrects something, go and correct it in the same commit.**
2. **Overstating a source in the direction that makes the argument better.** The Brief claimed S2L run *"two
   custom GPTs, a shared ChatGPT account across fifty people, Google Forms and WhatsApp."* Reality: **one**
   GPT is live, the second is unbuilt, the shared account is *proposed not committed*, and **"Google Forms"
   appears in no source at all — it was invented.** Worse, the S2L doc has a *"What changed in verification"*
   section naming the exact two errors that were then reintroduced. **When a number or quote makes the
   argument land, that is the one to re-read against the source.**
3. **Treating documentation as code truth.** Four code claims were written as fact from docs; two were
   wrong. The scheduler *does* create unassigned tasks; there are *no* task permission flags at all.
   **Verify against `rentok-backend` before a code claim enters a decision.**
4. **Reading absence of a workaround as absence of demand.** "Zero multi-select demand" was not a finding —
   multi-select has no detectable fingerprint, because the workaround looks like ordinary yes/no use.
5. **Filing research as a decision.** A CHANGELOG entry is for a call and its rejected alternative.
   *"We checked and we were right"* is not a decision. D80–D83 are already 21% of the file for 4 of 83
   decisions — the review gate flagged that trajectory as unsustainable.
6. **Defining the moat by looking at competitors.** Two answers were built and discarded that way before
   D80 landed on what the product does for the people it is built for.

---

## 7. Session lineage

Oldest first. Read memory first, transcripts last — **never read a raw `.jsonl` into context, they are
1MB+.**

```
3b149fc6 · ~/.claude/projects/-Users-eazypg-rentok-backend/3b149fc6-*.jsonl · repo created, docs moved out of rentok-backend
d920ecef · ~/.claude/projects/-Users-eazypg-rentok-backend/d920ecef-*.jsonl · same repo-creation work, parallel thread
aaba50c1 · ~/.claude/projects/-Users-eazypg-rentok-backend/aaba50c1-*.jsonl · grounding-notes + review-findings added; D1–D18 era
(parallel) · commit 2ff88c1, 2026-08-03 18:50 · v2.1 — deferred standing rules (D64), replaced suggest-close with show-status (D65), added F58 (D66). NOT this session; it landed while this one was running and took D64–D66.
cf1946ed · ~/.claude/projects/-Users-eazypg-rentok-backend/cf1946ed-8eaa-4035-8673-bf81c245987c.jsonl · THIS SESSION, 2026-08-03/04 · round-2 review (D67–D79), moat + story + problem + measurement (D80–D83), build sequence, engineering handoff, competitor research
```

**Note the parallel session.** Two sessions worked this repo the same evening and both reached "drop
standing rules" independently. That is why `review-round-2-decisions.md` numbers its decisions D64–D77
while the CHANGELOG has them as D67–D79 — the mapping is at the top of that file.

---

## 8. How to pick up prior work

In this order. Stop as soon as you have what you need.

1. **This file**, then **CHANGELOG.md** — decisions and their rejected alternatives.
2. **mem0** — `search_memories` for "task module", "RentOk task", "D80 moat".
3. **Auto-memory** — `~/.claude/projects/-Users-eazypg-rentok-backend/memory/project_task_module_redesign.md`.
4. **Obsidian** — `/obsidian query`, and `RentOk/S2L Account/` for the real customer's dependency maps.
5. **`@Past Chats`** and the lineage above — last resort, and never the raw JSONL.

**The S2L dependency maps** (`RentOk/S2L Account/2026-07-20 — Dependency Map.md`) are the single best
customer-evidence source in the project. **Read their "What changed in verification" section before quoting
anything from them** — it names which claims are fragile, and this project has already ignored it once.
