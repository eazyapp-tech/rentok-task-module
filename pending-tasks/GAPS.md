# Gaps

**Last live check: 16 Aug 2026.**

Ten hardcoded types can enter the **stack** today: joining, move-out, unassigned complaints, app update, payments not linked, move-in checklist, agreement renewals, food menu, expenses, tutorial.

- Money on the new home is **A2 only** (`payments_not_linked`). A1 and A3–A13 are not in this feed.
- The live joining card is **not B1**. B1’s new-home copy is switched off.
- D3 (inspections pending) is **not shown**. The push is commented out. The count may still be fetched.

**Home vs View All.** Home shows one card at a time (stack / swipe). View All is the full-screen hub by category. Live code may only emit ~10 types into that feed (the stack and the hub share it). This pack still catalogs all 65 for the hub we want. Do not treat the catalog as “all 65 on home at once.”

Cards stay as claimed. Do not use this file to rewrite Status lines.

The rest of this file is the 21 Jul 2026 parent copy, then the 16 Aug notes that sit under it.

---

## What the parent said changed on 21 Jul 2026

Copied from the parent's verification note:

- The pending-tasks feed was rewritten into a block-based v2 system. **All line numbers in this doc are stale.**
- **"No role filtering" is now FALSE** — per-property permission gating shipped (`resolveVisibilityContext` + `TeamMemberProperty`, gating on `can_view_*`). Ignore that gap.
- **~10 live task types today** (not 11), all still hardcoded. `inspection_pending` and `move_in_request_pending` are commented-out dead code.
- Still accurate: no task config table, no ranking, no assignee, no multi-property attribution, no top-N, no urgency text, no search. The permission-flag names are all real (~90 flags on `team_member_property`; the feed uses ~6).
- Realizing the full 65-entry registry is tracked at eazyapp-tech/rentok-backend#6249.

Parent totals as claimed (do not reconcile here): **64 active + 1 deprecated = 65 registry entries** (11 live in new system + 16 live in old system only + 36 new + 1 deprecated + 1 cross-listed B9/F5).

Note: B9 (Tenants to install the app) and F5 (Tenants not on the app) share the same underlying data. Show only one at a time based on the active category tab. That is a parent claim, not a code check. Product: on home they never see both at once (one stack card). On View All they sit on different category chips (People vs Growth). Same list, two names, still not merged. See [NEED-YOU People 1](NEED-YOU.md).

---

## Implementation phases (copied)

**Phase 1 — Migrate existing tasks (0 new queries, just wire existing logic)**

16 tasks from old quick_filter system that need migration to new homepage: A1, A3, A4, A5, B3, B4, B5, B6, B7, B9, C2, C3, C4, C5, C6, C7, D6, F1.

The parent says **16** and then lists **18 IDs**. Both kept. Not resolved here.

**Phase 2 — Quick wins (simple COUNT queries on existing entities)**

15 new tasks with low complexity: A6, A7, A8, A10, A11, B8, B10, B11, D2, D4, D8, D9, D10, E5, E6.

**Phase 3 — Medium lift (cross-table joins, balance calculations, or threshold logic)**

12 new tasks with medium complexity: A9, A12, A13, B12, B13, C8, C9, D7, D11, E7, E8, E9.

**Phase 4 — Growth & platform (new entity setup or external integration)**

6 new tasks: E10, E11, F2, F3, F4, G2.

**Infrastructure prerequisites (as claimed):**

- Role-based task filtering (extend `VisibilityContext` to check `Required Access` per task)
- Priority sort (add `compareTasks()` sort before returning the task array)
- Assignee resolution (query `team_member_property` for matching permissions)
- Multi-property aggregation labels ("from N properties")
- Task configuration table (move from hardcoded to DB-driven registry)

---

## Structural gaps: PRD vs. code (copied)

These architectural changes are prerequisites before scaling to the full registry. Verified against `master` on **21 Jul 2026**.

| Gap | What the target spec wants | What the code does today | Severity |
|---|---|---|---|
| No task config table | "Product/ops can add, remove, rename, reorder without an app release" | The ~10 live task types are hardcoded inline (`tasks.push({...})`). Adding one = code change + deploy. | Critical |
| No priority/ranking | "Backend-ranked shortlist (top N)" | Tasks are emitted in code order. No scoring, no dynamic tier. See Priority Framework in the parent / [RULES.md](RULES.md). | High |
| No assignee visibility | "Admin sees who is responsible for each task" | No assignee info on any task card. See Assignee Visibility in the parent / [RULES.md](RULES.md). | High |
| No multi-property attribution | "When viewing multiple properties, show which properties contribute" | Counts are aggregated but no "from N properties" label. See Multi-Property Aggregation in the parent / [RULES.md](RULES.md). | Medium |
| No top-N truncation | "Homescreen shows top N, not the full task universe. N is backend-configurable." | All tasks with count > 0 and not dismissed are shown. No limit. | High |
| No per-task icon | "Icon: required" per card schema | The new `tasks[]` has no icon field (only a per-category icon array exists). | Medium |
| No urgency text | "Urgency text or due-state copy: optional" per card schema | Field doesn't exist in the card object. No task emits urgency text. | Medium |
| No View All search | "Search input at the top" of View All | Same endpoint serves both surfaces. No search parameter in schema. | Medium |

Parent note, copied: **Role filtering is NOT a gap. It shipped.** An earlier version of the table listed "no role filtering" as Critical. That is now false. Per-property permission gating is live: `resolveVisibilityContext` resolves owner-vs-team-member and per-property permissions from `TeamMemberProperty`, and every task is gated on `can_view_*`. The feed gates on ~6 of the ~90 permission flags today; extending to finer-grained flags is future work, but the mechanism exists.

---

## Remaining code ground (later)

Do not fill this in during the Money slice.

- Card-by-card as-built vs claimed Status
- Whether A1-A13 line numbers still point at anything
- Whether ~10 vs 11 vs the appendix live counts can be made to agree
- People through Platform as-built

Until that pass, treat [MONEY.md](MONEY.md) as the claimed spec, and this file as the parent's 21 Jul 2026 snapshot of the gaps.

---

## Additional check — 16 Aug 2026

Light look at `getPendingTasks()` in `src/v1/homepage/service.ts` and the old-system widgets in `getAllTenants.ts`. Not a full audit. Does **not** change card Status lines. Parent date above stays 21 Jul 2026.

- Money on the new homepage is still only A2 (`payments_not_linked`). A1 and A3–A13 are not in this feed.
- Parent line numbers for A2 are stale. Query is ~L1403–1411. Card push is ~L1616–1626. Parent said ~L1371 / ~L1584.
- A2 live permission is `can_view_invoices` only. The registry also lists `record_payment`.
- `inspection_pending` and `move_in_request_pending` are still commented out.
- Live types still look like ~10 hardcoded pushes: joining, move-out, unassigned complaints, app update, payments not linked, move-in checklist, agreement renewals, food menu, expenses, tutorial.
- Live feed has a TODO `refund_request_pending` with no query. That is not A5 (deposits to refund).
- A1 old-system pointer `getAllTenants.ts:2670` is stale. That line is now agreement-renewals SQL. The rent-overdue widget is around L2806–2812. Count SQL around L2629 still matches the registry (active tenant, unpaid rent, >30 days). Amount SQL around L2643 sums all unpaid invoices for active tenants at the property — it does not restrict to the same rent-30+ people the count uses.
- A1 tap filter_code 5001 (~L4314) is any unpaid invoice (`status = 0` and `amount > 0`). It is not rent-30+ defaulters as the card claims.
- A12 jump: product is 5 business days ([NEED-YOU #2](NEED-YOU.md), locked B 16 Aug 2026). The registry card also listed calendar "5 days". That leftover sits on [A12](MONEY.md#a12-settlement-still-processing-money).
