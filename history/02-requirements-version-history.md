---
title: "Feature Requirements: version history"
date: 2026-09-05
status: "historical, moved out of 02-requirements.md on 2026-09-05"
tags: [rentok, tasks, history]
---

# Feature Requirements: version history

The five "What changed in version" blocks that used to sit at the end of [02-requirements.md](../02-requirements.md), in numeric order. Wording unchanged. The current version is in that file's frontmatter.

## What changed in version 2.0

Reconciled against all 63 decisions. **F23 (shift handover) is removed** — it is reassignment (D34). Compound requirements were split so each can be cut independently (F15a/b, F24a/b/c, F25a/b, F33a/b). **Eighteen requirements were added** that the decisions created but the list never captured — including both prerequisites, runner identity, camera-only, template versioning, areas, starter routines, rule management, and the people-lifecycle items. Every requirement now carries what the operator loses without it, and the tags were replaced with a cut order ranked by user-miss rather than by build cost.

## What changed in version 2.1

Stress-tested after v2.0 and cut. **Standing rules are deferred entirely** (D64) — F6, rule management, preview and guards all go, along with the stored room-occupancy prerequisite. **A task no longer suggests that work is done; it shows the live state of the thing it is linked to** (D65) — that removes a hand-written rule for every kind of linked thing, each of which could be wrong. **An alert can now be turned into work, one task per item** (D66, F58) — the small bridge that lets a detected problem become work with an owner. Operator-managed areas moved to V1.1; complaint matching became "show the open ones and let the person choose"; checklist editing is blocked while tasks are open rather than version-pinned.

*(The v2.1 line "vacant-room readiness has no home in V1" is superseded by v2.2 — F3 is pulled in.)*

## What changed in version 2.2

An adversarial round-2 review, worked finding by finding (D67–D79; log at [the round-2 review, 3 Aug 2026](2026-08-03-review-round-2.md)).

**The bands gained a rule** (D67): if a Band B feature would produce a false record without it, or would break another part of RentOk, it is Band B. That moved **five items up from C** — F53, F15a, F24c, F33a, F54 — each of which was letting a Band B feature lie. **F8 also moved up** (D68), which is what D45 had already decided, and the Brief stopped calling the entity link ship-blocking. **F47 and F25a moved down to C** (D78).

**Two v2.1 calls were amended.** F48/F49/F50 are **kept**, reframed as managing ordinary repeating tasks — under D35 a rule *is* a repeating task, so F5 needs them regardless. And **F3 is pulled into this cycle**, so vacant-room readiness does have a home: a finished move-out creates the prep task, using a seam that already exists.

**Two locked decisions were corrected.** D13 would have shipped every permission open to everybody, making D55 false in production on day one — staff now default to "see only my own" (D69). D22 promised the manager a first look that nothing implemented — she now sees the same exception list about her property that the owner sees (D71).

**Also:**
- Per-person numbers come from fan-out only, never from pooled self-taps (D70)
- Notifications gained a reminder before the deadline, and a send time set per property (D73)
- Photos are deleted from the phone after they upload (D75)
- Translation is deferred and the manager writes in her own script, but starter templates ship bilingual (D76)
- Duplicate tasks are explicitly allowed (D77)
- New properties get their routines created unassigned, and the scheduler must skip routines with nobody on them (D78, P0)

**Three review findings were wrong and were corrected by Sanchay or by a code check:** the monthly building audit is one form, not 200 tasks (D72) · staff do not share phones, which invalidates a persona claim carried in three docs (D74) · and "unassigned means nothing runs" was not true of the scheduler, so D78 needs a real code change rather than none.

## What changed in version 2.3

The story widened to the property's whole work, not its routines (D81) — which added **F59** (a visit log: one task that records arriving and leaving), tightened **F8** to say history shows the submitted answers and not only that a task happened, and put high-frequency logs and licence-expiry tracking explicitly in *Not building*. The problem is now stated as late discovery rather than lazy staff (D82), and the cost chain behind it is measured rather than asserted (D83). **F51 moved C → B** — a resignation currently leaves open work going overdue against someone who has left, which makes F21 and F22 report failures about a person who no longer works there.

## What changed in version 2.4

**The question types are committed (D84)** — the full set is settled rather than deferred, which gives **F36** a fixed list to validate against and needs no second pass later. This adds two migrations: **M5** (rename `rating_5`/`rating_10`/`dropdown`, which live data uses and the code does not know) and **M6** (`structure` gains sections and branching, which a flat array cannot hold). **F29's build note is corrected** — an earlier version called a 1–5 rating "a five-option select"; it is its own type carrying a scale, because F21 has to average it. **Custom categories belong to the account, not the property (D85)**, which settles M3's open sub-question and unblocks **F32**. **P1's description is corrected**: a scheduler does fire today, it is simply registered nowhere — the job is to find and authenticate it, not to build one. Verified against live data 5 Aug 2026: **190 of 394 checklists (48.2%) carry a type the code does not know**, not the 12.7% recorded earlier, which was the share of questions rather than checklists.

## What changed in version 2.5

- **2026-09-05.** Code citations removed from P0, P1, F26, F29, M2, M5 and M6, per the house rule that a requirements doc says what the operator must have and leaves the shape (files, columns, flags) to the spec. Each claim keeps its meaning in plain words; the evidence moved to `reference/grounding-notes.md` section 8 and the spec's "Today" lines. No requirement changed.
