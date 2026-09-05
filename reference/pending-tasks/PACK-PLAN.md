# Pending-tasks pack — implementation plan

**Goal:** Turn the home-screen needs-attention registry (`getPendingTasks`) into a pack a manager can read: front door, once-only RULES, a scan INDEX, Money A1–A13, People B1–B13, Compliance C1–C9, Property D1–D11, Daily Ops E1–E11, Growth F1–F5, Platform G1–G3, and a GAPS file that is the live-vs-claimed check (last checked 16 Aug 2026).

**Architecture:** One job per file. Cards copy what the parent registry *claims*. Live-vs-code lives only in GAPS. The vault is a later mirror. This folder is not the checklist / task / schedule Task module in the rest of this repo.

**Tech Stack:** Markdown in `eazyapp-tech/rentok-task-module`. Source file: `pending-tasks/sources/pending-tasks-registry.md` (git-mv from `references/`). Voice: situation first, everyday words, locked A1 card shape.

## Global Constraints

- This pack is the home-screen needs-attention feed (`getPendingTasks`). It is not the checklist / task / schedule Task module. Say that on both front doors.
- Cards copy what the registry claims. Do not update Status from code.
- If Task name and title pattern disagree, keep both on the card.
- Vault is a mirror later. This repo wins over vault / Notion.
- Vocabulary: checklist · task · schedule · staff / manager / owner · beds. Never SOP, landlord, units, seamless, leverage.
- No em-dash habit in our prose. Quotes from the parent may keep theirs.
- No commit unless Sanchay asks. No push.
- All category files are written (Money through Platform). Money NEED-YOU is locked 16 Aug 2026. People NEED-YOU closed. Both picks locked 16 Aug 2026. People is written. Compliance is written. Compliance: no new calls. Property is written. Property D5 locked A 16 Aug 2026. D5 = room walkthrough. C5 = papers. Daily Ops is written. Daily Ops: no new calls. E11 is a salary reminder. Locked NEED-YOU #3 A+C. Growth is written. Growth: no new calls. F5 is the Growth chip for the same list as B9. Locked NEED-YOU People 1 A. Platform is written. Platform: no new calls. G3 is deprecated. Do not write more categories.

---

## File map

| Path | Responsibility |
|---|---|
| `README.md` (repo root) | One Related line only. Do not mix this feed with checklist / schedule. |
| `pending-tasks/README.md` | Front door. What this is / isn't. How to read. Repo wins. |
| `pending-tasks/PACK-PLAN.md` | This file. Remaining work after slice 1. |
| `pending-tasks/sources/pending-tasks-registry.md` | Untouched parent. git-mv from `references/`. Do not dress it up. |
| `pending-tasks/INDEX.md` | All 65 IDs. One line each. Feature-gates and ship phases as claimed. |
| `pending-tasks/RULES.md` | T1–T5, colour, sort, promotion, who sees what, assignee chip, multi-property. Once. |
| `pending-tasks/MONEY.md` | A1–A13 complete. Manager voice pass 16 Aug 2026. |
| `pending-tasks/PEOPLE.md` | B1–B13 complete. 16 Aug 2026. |
| `pending-tasks/COMPLIANCE.md` | C1–C9 complete. 16 Aug 2026. |
| `pending-tasks/PROPERTY.md` | D1–D11 complete. 16 Aug 2026. |
| `pending-tasks/DAILY-OPS.md` | E1–E11 complete. 16 Aug 2026. E11 salary reminder. |
| `pending-tasks/GROWTH.md` | F1–F5 complete. 16 Aug 2026. F5 Growth chip, same list as B9. |
| `pending-tasks/PLATFORM.md` | G1–G3 complete. 16 Aug 2026. G3 deprecated. |
| `pending-tasks/NEED-YOU.md` | Money: five picks, locked 16 Aug 2026. People NEED-YOU closed. Both picks locked 16 Aug 2026. Compliance: no new calls. Property D5 locked A 16 Aug 2026. D5 = room walkthrough. C5 = papers. Daily Ops: no new calls. E11 stays locked NEED-YOU #3 A+C. Growth: no new calls. F5 stays locked NEED-YOU People 1 A. Platform: no new calls. G3 stays deprecated. |
| `pending-tasks/GAPS.md` | Live vs claimed. Last checked 16 Aug 2026. Cards stay as claimed. 21 Jul 2026 kept as history. |

---

### Task 1: Move the parent file, do not edit it

**Files:**
- git-mv: `references/pending-tasks-registry.md` → `pending-tasks/sources/pending-tasks-registry.md`
- Create stub at old path only if some other file in this repo still links the old path

- [x] **Step 1:** `git mv` the registry. Confirm the original path is gone.
- [x] **Step 2:** Grep this repo for the old path. If a README still points there, add a 4-line stub that points to the new file. Prefer gone.

**Done when:** the parent file lives under `pending-tasks/sources/` and has zero content edits.

---

### Task 2: Front doors

**Files:**
- Modify: `README.md` (repo root) — one Related line
- Create: `pending-tasks/README.md`

- [x] **Step 1:** Add one Related line on the repo README: needs-attention pack lives in `pending-tasks/`. Not checklist / schedule.
- [x] **Step 2:** Write `pending-tasks/README.md`: what this is, what it is not, how to read (INDEX → RULES → category file), this repo wins over vault / Notion, parent file is not to be edited.

**Done when:** both front doors say this is `getPendingTasks`, not the Task module redesign.

---

### Task 3: RULES once

**Files:**
- Create: `pending-tasks/RULES.md`

- [x] **Step 1:** Write T1-T5, colour, sort, promotion, who sees what, assignee chip, multi-property. Copy claims from the parent. Cards must not repeat this essay.

**Done when:** a Money card can say "see RULES" for multi-property and sort without losing a field.

---

### Task 4: INDEX of every ID

**Files:**
- Create: `pending-tasks/INDEX.md`

- [x] **Step 1:** One line per registry ID (A1-G3). Everyday name, claimed status, link to card heading.
- [x] **Step 2:** Feature-gates and ship phases as the parent claims. No card bodies.
- [x] **Step 3:** Future category files linked with "not written yet".

**Done when:** 65 lines exist and a scan does not require opening MONEY.md.

---

### Task 5: MONEY cards A1–A13

**Files:**
- Create: `pending-tasks/MONEY.md`

Card shape (locked, clone A1):

1. `## {ID}. {everyday name} (Money)`
2. **Registry status**
3. **The situation**
4. **What the card shows** (keep title clashes, do not pick)
5. **Who sees it**
6. **How we count**
7. **Missing from the registry** (or omit)
8. `<details>For engineering</details>`

- [x] **Step 1:** Write A1 to match the locked sample voice. Do not "improve" the shape.
- [x] **Step 2:** Write A2-A13 the same way. Every field from the parent appears or is listed as missing. No placeholders.
- [x] **Step 3:** Inventory diff against the parent dump (Task Name through Status, plus A13 Cross-Reference). Silent drop = not done.

**Done when:** 13 cards, no skeletons, inventory diff clean.

Voice pass (16 Aug 2026): manager-facing blocks shifted to home-screen register. Product traps kept on cards. Forks that change the product live in [NEED-YOU.md](NEED-YOU.md). Not a silent resolve.

---

### Task 6: GAPS (live vs claimed)

**Files:**
- Create / replace: `pending-tasks/GAPS.md`

- [x] **Step 1:** Date the stub. Last code check as the parent says: 21 Jul 2026.
- [x] **Step 2:** Copy structural-gaps + implementation-phases claims from the parent. Do not re-audit the codebase. Do not "fix" cards from code. Say remaining code ground is later.
- [x] **Step 3:** Sanchay ruled A: refresh GAPS against live code. 16 Aug 2026. Cards unchanged. 21 Jul kept as history.

**Done when:** a reader can see what is live on the new home vs old list chips vs claimed, without treating MONEY.md as as-built.

---

### Task 7: Slice-1 verify (no commit)

- [x] **Step 1:** Count A1-A13 = 13.
- [x] **Step 2:** Confirm git-mv happened.
- [x] **Step 3:** Grep our new files for SOP / landlord / units / seamless / leverage.
- [x] **Step 4:** Leave the working tree dirty. Do not commit. Do not push.

**Done when:** Sanchay can open `pending-tasks/MONEY.md` and `pending-tasks/NEED-YOU.md`. People waits on those rulings.

---

### Task 8: PEOPLE cards B1–B13

**Files:**
- Create: `pending-tasks/PEOPLE.md`
- Modify: `pending-tasks/INDEX.md`, `pending-tasks/PACK-PLAN.md`, `pending-tasks/NEED-YOU.md`, `pending-tasks/README.md`

Card shape: clone Money (locked A1 shape). Counted title is the product title. Short names in the engineering fold. Count only. No ₹.

- [x] **Step 1:** Write B1–B13. Every parent field on the card, in the fold, or under Missing. No skeletons.
- [x] **Step 2:** Keep B2 / B10 / B12, B7 / B8, B9 / F5 as separate cards. Guest visits: "only if guest visits are on".
- [x] **Step 3:** Append People NEED-YOU (at most 5). Update INDEX links. People written. People NEED-YOU closed 16 Aug 2026.

**Done when:** 13 cards, manager-facing has no pack-writer talk, Sanchay can kill/keep People voice.

---

### Task 9: COMPLIANCE cards C1–C9

**Files:**
- Create: `pending-tasks/COMPLIANCE.md`
- Modify: `pending-tasks/INDEX.md`, `pending-tasks/PACK-PLAN.md`, `pending-tasks/NEED-YOU.md`, `pending-tasks/README.md`

Card shape: clone Money (locked A1 shape). Counted title is the product title. Short names in the engineering fold. Count only. No ₹. Feature gates as "only if … is on".

- [x] **Step 1:** Verify People against Money voice. Gate 1 passed 16 Aug 2026. B2 button is Review Now. B9 names F5 as the other chip, never both at once.
- [x] **Step 2:** Write C1–C9. Every parent field on the card, in the fold, or under Missing. No skeletons. Keep C3 / C4 and C5 / C6 as separate cards. C8 / C9: "only if KYC / e-sign is on".
- [x] **Step 3:** Append Compliance NEED-YOU (none). Update INDEX links. Compliance written. Next Property after Sanchay.

**Done when:** 9 cards, manager-facing has no pack-writer talk, Sanchay can kill/keep Compliance voice.

---

### Task 10: PROPERTY cards D1–D11

**Files:**
- Create: `pending-tasks/PROPERTY.md`
- Modify: `pending-tasks/INDEX.md`, `pending-tasks/PACK-PLAN.md`, `pending-tasks/NEED-YOU.md`, `pending-tasks/README.md`

Card shape: clone Money (locked A1 shape). Counted title is the product title. Short names in the engineering fold. Count only. No ₹. Feature gates as "only if … is on".

- [x] **Step 1:** Verify Compliance against Money / People voice. Gate 1 passed 16 Aug 2026. C3 / C4 and C5 / C6 stay two jobs. C8 presence-only like A9.
- [x] **Step 2:** Write D1–D11. Every parent field on the card, in the fold, or under Missing. No skeletons. Keep D1 / D2 / D8 / D9 and D3 / D4 and D5 / D6 as separate cards. D5 / D6 / D7 / D11: "only if … is on".
- [x] **Step 3:** Append Property NEED-YOU. D5 locked A 16 Aug 2026. D5 = room walkthrough. C5 = papers. Update INDEX links. Property written.

**Done when:** 11 cards, manager-facing has no pack-writer talk. Property D5 locked A 16 Aug 2026. D5 = room walkthrough. C5 = papers.

---

### Task 11: DAILY OPS cards E1–E11

**Files:**
- Create: `pending-tasks/DAILY-OPS.md`
- Modify: `pending-tasks/INDEX.md`, `pending-tasks/PACK-PLAN.md`, `pending-tasks/NEED-YOU.md`, `pending-tasks/README.md`

Card shape: clone Money (locked A1 shape). Counted title is the product title when N exists. Presence only when there is no N. Short names in the engineering fold. Count only. No ₹. Feature gates as "only if … is on".

- [x] **Step 1:** Verify Property D5 against Money voice. Gate 1 passed 16 Aug 2026. D5 is the room. Title `{N} Move-In Checklist(s) Pending`. Button Review Now. Tap filter 2384. C5 stays KYC.
- [x] **Step 2:** Write E1–E11. Every parent field on the card, in the fold, or under Missing. No skeletons. Keep E5 / E6 / E7 / E8 as separate cards. E2 / E5 / E6 / E7 / E8 / E9 / E10: "only if … is on". E11 is a salary reminder. Product button Review Now. Pay Now leftover in the fold. Not reimbursement (A13).
- [x] **Step 3:** Append Daily Ops NEED-YOU (none). Update INDEX links. Daily Ops written. Next Growth after Sanchay.

**Done when:** 11 cards, manager-facing has no pack-writer talk, Sanchay can kill/keep Daily Ops voice.

---

### Task 12: GROWTH cards F1–F5

**Files:**
- Create: `pending-tasks/GROWTH.md`
- Modify: `pending-tasks/INDEX.md`, `pending-tasks/PACK-PLAN.md`, `pending-tasks/NEED-YOU.md`, `pending-tasks/README.md`, `pending-tasks/PEOPLE.md` (B9 fold now links F5), `pending-tasks/RULES.md`

Card shape: clone Money (locked A1 shape). Counted title is the product title. Short names in the engineering fold. Count only. No ₹. Feature gates as "only if … is on".

- [x] **Step 1:** Verify Daily Ops against Money / People voice. Gate 1 passed 16 Aug 2026. E1–E11 present. Counted titles where N exists. Presence only where no N. E11 is a salary reminder (Review Now, not Pay Now as product). E5 is the door tonight, not leaving for good. Engineering folded.
- [x] **Step 2:** Write F1–F5. Every parent field on the card, in the fold, or under Missing. No skeletons. F1 / F4: "only if … is on". F5 is the Growth chip for the same list as B9. Title `{N} Tenant(s) Not On App`. Never both at once. Do not merge names. Locked NEED-YOU People 1 A.
- [x] **Step 3:** Append Growth NEED-YOU (none). Update INDEX links. Growth written. Next Platform after Sanchay.

**Done when:** 5 cards, manager-facing has no pack-writer talk, Sanchay can kill/keep Growth voice.

---

### Task 13: PLATFORM cards G1–G3

**Files:**
- Create: `pending-tasks/PLATFORM.md`
- Modify: `pending-tasks/INDEX.md`, `pending-tasks/PACK-PLAN.md`, `pending-tasks/NEED-YOU.md`, `pending-tasks/README.md`, `pending-tasks/RULES.md`

Card shape: clone Money (locked A1 shape). Counted title when N exists. Presence only when there is no N. Short names in the engineering fold. Compact ₹ only if a real formula exists, else count. Feature gates as "only if … is on". G3 is deprecated: short card, not a live home job.

- [x] **Step 1:** Verify Growth against Money / People voice. Gate 1 passed 16 Aug 2026. F1–F5 present. F5 title `{N} Tenant(s) Not On App`. Names People B9 as the other chip, never both. Counted titles. No fake ₹. User-first. Engineering folded.
- [x] **Step 2:** Write G1–G3. Every parent field on the card, in the fold, or under Missing. No skeletons. G1 counted days title. G2 presence. G2: "only if WhatsApp is on". G3 deprecated. Does not show. Leftover in fold. Do not reopen as live.
- [x] **Step 3:** Append Platform NEED-YOU (none). Update INDEX links. Platform written. All categories written.

**Done when:** 3 cards (G3 short/deprecated), manager-facing has no pack-writer talk. Pack categories complete (Money through Platform).

---

## Remaining work (not this slice)

All categories written (Money through Platform). People NEED-YOU closed. Both picks locked 16 Aug 2026. Compliance: no new calls. Property D5 locked A 16 Aug 2026. D5 = room walkthrough. C5 = papers. Daily Ops: no new calls. E11 is a salary reminder. Locked NEED-YOU #3 A+C, 16 Aug 2026. Growth: no new calls. F5 is the Growth chip for the same list as B9. Locked NEED-YOU People 1 A, 16 Aug 2026. Platform: no new calls. G3 is deprecated. **GAPS refresh done 16 Aug 2026.** Remaining is Sanchay read (README + NEED-YOU + GAPS) + commit when he says. No vault mirror unless already there. Do not write more categories.

| Order | File | IDs | Gate |
|---|---|---|---|
| 2 | `PEOPLE.md` | B1–B13 | **Done** 16 Aug 2026. People NEED-YOU closed. People 2 locked B (Review Now) |
| 3 | `COMPLIANCE.md` | C1–C9 | **Done** 16 Aug 2026. Compliance: no new calls |
| 4 | `PROPERTY.md` | D1–D11 | **Done** 16 Aug 2026. Property D5 locked A (room walkthrough). C5 stays KYC |
| 5 | `DAILY-OPS.md` | E1–E11 | **Done** 16 Aug 2026. Daily Ops: no new calls. E11 salary reminder. Locked NEED-YOU #3 A+C |
| 6 | `GROWTH.md` | F1–F5 | **Done** 16 Aug 2026. Growth: no new calls. F5 Growth chip, same list as B9. Locked NEED-YOU People 1 A. Do not merge names |
| 7 | `PLATFORM.md` | G1–G3 | **Done** 16 Aug 2026. Platform: no new calls. G3 deprecated. All categories written |
| later | `GAPS.md` fill | live-vs-code per card | **Done** 16 Aug 2026. Live check. Cards unchanged |
| later | Vault mirror | `RentOk/` copy | after repo pack is the source |

## Slice-1 done checks

- [x] Parent git-mv'd, not edited
- [x] Both front doors: needs-attention feed, not checklist / schedule
- [x] RULES written once
- [x] INDEX has all 65 IDs
- [x] MONEY has A1-A13, no placeholders
- [x] Field inventory A1-A13 vs parent is clean
- [x] GAPS stub dated 21 Jul 2026, copied not re-audited. Additional light check 16 Aug 2026 appended
- [x] GAPS refresh against live code 16 Aug 2026. Cards unchanged. 21 Jul kept as history. Remaining: Sanchay read + commit when he says
- [x] MONEY manager voice pass 16 Aug 2026. Product traps kept on cards
- [x] NEED-YOU.md: five picks, A/B, recommendation
- [x] NEED-YOU #3 locked A+C 16 Aug 2026. A13 reimbursement-only. E11 salary reminder. Product button Review Now. Pay Now leftover in the fold.
- [x] NEED-YOU #5 locked A 16 Aug 2026. Count only until a real ₹ formula exists. A6 title is `{N} Settlement(s) Failed`. A9 count or presence only, no amount sort
- [x] PEOPLE.md B1–B13 written 16 Aug 2026. INDEX links updated. People NEED-YOU closed.
- [x] NEED-YOU People 1 locked A 16 Aug 2026. B9 is the People chip. F5 is the Growth chip for the same list, not a second People card. Do not merge names
- [x] NEED-YOU People 2 locked B 16 Aug 2026. B2 button is Review Now. Tap is still the leaving / move-out request list. Settle Dues is a registry leftover in the engineering fold only. This is a yes on leaving, not collect. Permission stays eviction.
- [x] COMPLIANCE.md C1–C9 written 16 Aug 2026. INDEX links updated. Compliance: no new calls.
- [x] PROPERTY.md D1–D11 written 16 Aug 2026. INDEX links updated. Property D5 locked A 16 Aug 2026. D5 = room walkthrough. C5 = papers.
- [x] NEED-YOU Property D5 locked A 16 Aug 2026. D5 is the room walkthrough (title `{N} Move-In Checklist(s) Pending`, button Review Now, tap live filter 2384). C5 stays KYC. Verification / Verify Now / spec tap KYC are registry leftovers in the engineering fold. Do not retarget to filter 201.
- [x] DAILY-OPS.md E1–E11 written 16 Aug 2026. INDEX links updated. Daily Ops: no new calls. E11 is a salary reminder. Product button Review Now. Pay Now leftover in the fold. Not reimbursement (A13). Locked NEED-YOU #3 A+C.
- [x] GROWTH.md F1–F5 written 16 Aug 2026. INDEX links updated. Growth: no new calls. F5 is the Growth chip for the same list as B9. Title `{N} Tenant(s) Not On App`. Never both at once. Do not merge names. Locked NEED-YOU People 1 A.
- [x] PLATFORM.md G1–G3 written 16 Aug 2026. INDEX links updated. Platform: no new calls. G3 deprecated. Does not show. All categories written (Money through Platform).
- [x] No commit
