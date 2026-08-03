---
title: "Task Module — Top 1% Redesign Brief (v0 ORIGINAL, superseded)"
date: 2026-07-18
tags: [rentok, brief, tasks, staff, module-redesign, superseded]
owner: Sanchay
status: superseded
companion: Task Module - Feature Gap Audit.md
---

> # 📎 v0 ORIGINAL — kept for history
>
> The first working draft of the Task Module brief (2026-07-18), preserved because it is where the scope, the moat framing, and the 18 must-ships were first worked out. **It is superseded** by `../Task Module Brief.md`.
>
> **Two things in here are known-wrong:** (1) the **"five outward write-backs"** model — a task does not write into dues/KYC/assets; it reads the linked thing's state and *suggests*; (2) it frames the **Checklist Library as a parallel workstream** — it is one requirement inside this redesign.
>
> **Read instead:** `../CHANGELOG.md` (source of truth), `../feature-requirements.md` (F1–F40), `../Task Module Brief.md`.

# Task Module — Top 1% Redesign Brief

## In one line

Turn the Task module from a single-kind checklist app into the staff's proof tool — where proof protects the person who collected it, failures become actions, work connects to rent/deposit/complaints, and tasks arrive on time and find the right person, without making staff feel watched.

## The user

This is a warden-and-staff module, not a manager-and-reports module.

- **The warden / manager on site (Priya).**[^priya] Lives at the property. Owner calls many times a day. No clean way today to prove she did her job when a tenant complains the room is dirty. Reads Hindi better than English. Her biggest fear — documented — is that a tool the owner uses to watch her will threaten her job, and she will quietly sabotage it.
- **Housekeeping and cleaning staff.** Low-literacy, cheap Android. The WhatsApp link is their only way in. They do the work but rarely fill the form.
- **The security guard (Ramu).**[^ramu] Night shift, alone, paper register today. Needs Hindi / Telugu / Kannada. Will use a tool that makes his job feel respected; will resist one that replaces his register with a watch.
- **The owner off-site.** Wants to know what happened today without calling five people. Reads what the module produces; does not fill checklists.

A manager at a large, structured operator (the Covie / Stanza tier — 45 of 64 current task users are in the Structured/Large team-size tiers, 16+29[^grounding]) also judges the module against a bespoke audit suite they run today.

[^priya]: Composite persona, sourced. Verbatim from the Persona Bible (`RentOk Marketing WIKI/01_Market_Intelligence/icp_and_personas.md:178`): *"If Priya sees RentOk as a surveillance tool that threatens her job, she will sabotage adoption. Must be positioned as 'your assistant that makes the owner trust you more.'"* Line 234 names "Threatened Manager (Priya)" as a top-3 deal-blocker.
[^ramu]: Composite. Persona Bible line 192: *"If the app replaces his paper register, he may feel threatened. Position as 'modern security tools that make your job respected.'"*
[^grounding]: Live data, Metabase 2026-07-17: 73 properties, 64 managers, 82 active checklists, 559 questions. Team-size split Solo 8 / Small 11 / Structured 16 / Large 29. Field-type mix: text 48%, yes/no 38%, photo 6%, rating 5%, dropdown 4%.

## The bet

Every rival in this category — MaintainX is the clearest example — gets wrong the relationship with the person filling the form. Every rival is a watch-tool with friendly paint. Staff complete them because the boss is watching. Adoption is shallow, gaming is rampant, and the proof collected is proof *against* the person who collected it.

Our bet: **proof that protects the person who collected it.** When Priya cleans a room at 9am and the tenant complains at 4pm, the photo and the time-stamp are *her* defense, not the owner's accusation. The owner sees the exception; the staff sees their record.

This shapes what we refuse to build as much as what we build. We will not ship a built-in fines system this cycle or next: the moment staff feel the tool can deduct from their pay, they stop filling it honestly, and the proof collapses for everyone.

T1 below treats this as a launch requirement to test, not an assumption to ship on.

## How this brief was built

The full feature-space audit lives in `Task Module - Feature Gap Audit.md`. Fifteen domains, ~250 atomic capabilities, each scored 0–3 against the actual code. Every *gap-audit* claim traces to a row in that audit; every row in the audit traces to a file:line in the backend. The moat-build targets in §1a/§3 (write-backs and triggers) cite their adapter functions directly rather than appearing as audit rows. If a future question is "did we miss X?", the answer is in the audit, not in re-discovery.

The short version: the module scores **0 or 1 on the large majority of table-stakes capabilities** — due dates, reminders, escalation, approval, access control, signature, offline, insight, audit log. It is a single-kind (checklist), single-shot (one WhatsApp), single-direction (no write-back) product today. The redesign lifts every table-stakes gap to at least functional, and builds the differentiators our market position makes possible. Every gap-audit claim below traces to a row in the audit; the moat-build targets in §1a/§3 (the write-backs and triggers) cite their adapter functions directly.

## Three structural capabilities define this redesign

These are the levers that turn a checklist app into the operating layer of the property. Each is grounded in code, not speculation.

**1. Entity linking — the polymorphic columns exist, the engine to use them does not.** Every task instance carries `entity_id` + `entity_type` (`taskInstance.ts:37-41`); `entity_type` is a free varchar, so adding values needs no schema change. But "a free varchar on a column" is not an engine — today the scheduler writes only `'room'` and `'property'` to it, and the only reader that filters on it hardcodes `'room'`. The addressable universe is wider (tenant, team member, invoice, complaint, payment, KYC request all have UUID PKs, so they *can* be referenced), but realizing any of it is new scheduler code + new reader code, not a switch. Five write-back *targets* are designed (fail→complaint, KYC-done, invoice-paid, move-out deductions, asset condition), each with a precedent pattern to copy from `markMoveOutItem` (`moveOutChecklistService.ts:359`) and the move-out → complaint auto-raise at `moveOutChecklistService.ts:657`. **But none of this exists yet as task-module code.** Today `submitTask` (`taskController.ts:210-239`) only writes `status` + `responses` + lat/long — there is no hook layer, no dispatcher, no reusable write-back function. The hook layer is the build; the five targets are *designed*, not *ready*. Three more high-value targets — eviction/handover, gate-exit, asset inspection — are blocked on integer PKs and need one schema decision (widen `entity_id` from uuid to varchar) to unlock.

**2. Triggers — scheduled today, event-driven and rule-based next.** Today tasks come only from the scheduler (`/trigger` route, poll-based). There is no domain-event bus anywhere in the codebase (`EventEmitter` exists only for SSE streaming, not for status flips) — every status change is inline, so event-driven tasks mean inserting hooks at lifecycle events. The trigger-grounding pass identified ~22 candidate hook sites and bucketed them by insertion cost: LOW-cost single-funnel sites (invoice created, refund processed, asset added, complaint reassigned, move-out checklist complete — each has one clean insertion point); HIGH-cost sites (room vacated is computed via raw SQL with no single mutation point — needs a refactor or a periodic scan). That bucketing is preliminary, in this brief, not a separate ranked list. Rule-based tasks (conditional spawn, threshold trigger) build on the same hook layer. This is what makes a move-out automatically spawn a deposit-deduction checklist, or unpaid rent spawn a collection task — capabilities no rival can match because they don't own the property system.

**3. Approve/reject + access control — some schema in place, most of the work is not.** `task_instance` has dormant `rejection_reason`, `reviewed_by`, `reviewed_at` columns (zero writers today), and the status enum needs `approved`/`rejected` added. That's the schema that exists; what doesn't exist is the writer, the transition rules, the UI, and the permission check. The move-out checklist is the closest template — separate submit + approve flags with approve-is-superset-of-submit at `moveInMoveOutChecklistController.ts:258-272`. For access control, the task controller does **zero** `checkAuthInDb` calls today — 90 permission flags exist on `team_member_property` (verified by enumeration), none for tasks. We add four flags (`view_tasks`, `view_assigned_tasks`, `submit_tasks`, `approve_tasks`) and **retrofit every existing endpoint** with the inline-check pattern. Doing only the new endpoints would ship a half-gated module — worse than the current un-gated one.

## Showing up in the manager's app — reuse selectively, not blindly

There is already a fully-specced pending-tasks product in RentOk at `RentOk/Product/pending-tasks-registry.md` (Obsidian) — 65 entries across 7 categories with a T1-T5 priority ranking, source-type system, and permission table. But the Task module is a different beast (staff physically complete checklists vs the registry's cards that count things from other tables), so reading the existing spec *critically*:

**Reuse:** category chips + priority colors, `filter_code` deep-link router, dismiss service (Redis TTLs), rolled-up counts across properties, permission-table *concept*.

**Do NOT copy:**
- The T1–T5 *definitions* — money-shaped (T1 = revenue at risk). Need ops-shaped equivalents for tasks ("proof gap for a tenant complaint in flight", "compliance deadline today").
- The source-type system — most task cards are `task_instance` rows past `due_date`, not state computed from other tables.
- Assignee-via-permission — task assignment is explicit via `task_schedule_team_member`, not computed at read time.
- Most of the 65 entries — only D3/D4 are task-native today. The Task module adds 4 fresh cards, doesn't land inside a 65-entry feed.
- The T6 proactive layer — feature-config discovery ("enable payout"), different mental model entirely.

**Two distinct places this shows up:** (a) **manager-side cards** — extend the registry's Property category with Cleaning Overdue, Review Queue, Failed Checks This Week, Staff Tasks Overdue, using registry visual vocabulary + infrastructure but task-specific mechanics (counts from `task_instance` rows, explicit assignment, ops-shaped priority); deep-link to the webview wrapper (`webview_page.dart`) for the full board; (b) **staff-side worklist** — separate "my tasks today" list, different audience/scope/action, already in must-ship #4. The `inspection_pending` card (D3 in the registry, commented out at `v1/homepage/service.ts:1624-1635` but dismiss-map still wired) is the smallest proof-of-concept. Native bottom-nav Tasks tab is a multi-week build with a backend JSON API that doesn't exist — defer.

## What the module must do (information needs, by job)

Three jobs, kept separate so we design each for its own job. Each pairs with the broken-thing-it-fixes.

**Need 1 — "What needs my attention right now?" (today's pulse).** *Broken today:* the module is invisible inside the phone app the manager lives in, and staff have no inbox. *The need:* a manager opens first thing in the morning, last thing at night — in one glance, how many of today's tasks are done, how many pending, which failed (failures first, those are where the day's problems live). A staff member opening their phone gets the same answer for *their* work: your tasks today, the one you haven't done, tap to start.

**Need 2 — "What do I do next, and how do I prove I did it?" (the act of work).** *Broken today:* the form is a dead end, proof is half-built, there's no signature, the runner is online-only, and notifications fire once with no follow-up. *The need:* the form must be fast (PM estimate, not validated: under 30 seconds for a room cleaning), work where staff work (no login, cheap phone, low-signal basement, Hindi), and capture proof (photo, time, where taken, signature where it matters) as part of the act, not a separate step. **Must-meet constraint:** the runner must load fast and tolerate low signal — a staff member who taps the WhatsApp link in a basement and waits for a React page to hydrate gives up, blames the tool, and tells Priya "the link didn't work." Page-load, offline tolerance, and PWA behaviour are requirements, not colour.

**Need 3 — "What's the pattern, and what should I set up once?" (setup and insight).** *Broken today:* building a checklist is a blank box; there is no insight layer — the manager has raw submissions but cannot see which rooms fail most or which staff member completes reliably. *The need:* setup is the Checklist Library's job (in-flight[^library]); patterns — completion rate, failure rate by room/staff/template, trends — are this module's job.

[^library]: The Checklist Template Library project (31 templates, a recommendation engine, and a permission-flags blocker) is the dedicated workstream for the setup half of Need 3. See the `rentok-checklist-library/` repo + memory `project_checklist_library`. This brief treats the library as in-flight and does not re-spec it.

## What we must ship

Split into two lists, ranked by different things. The first is what the manager and staff would miss most. The second is what the system needs to make the first list real.

**The moat — one indivisible outcome, two build streams (top-ranked):**

1a. **The submitTask hook layer (ship-blocking for this cycle).** Two capabilities share one build inside `submitTask`: (i) fail→corrective task — needs a `fail` status on `task_instance` (currently `pending/submitted/expired`) + a fail-state read + a corrective-task spawn; (ii) five entity write-backs (fail→complaint, KYC-done, invoice-paid, move-out deductions, asset condition) — needs a dispatcher after the status flip, plus per-target adapter functions. These are genuinely one build — same hook, same dispatch frame. **One precedent is cited (`markMoveOutItem` at `moveOutChecklistService.ts:359`); the other four adapters are built from scratch** — the *target* functions exist in their modules (`useKYCCredit` at `kyc.ts:176`, `addPayment` at `payment.ts:1274`, `TeamPassbookExpenseService.add`, `markMoveOutItem`) but the wiring to `submitTask` does not.

1b. **The event-driven trigger layer (date-locked follow-up sprint, NOT this cycle).** A separate retrofit: hooks at ~22 lifecycle sites (invoice created, refund processed, complaint reassigned, move-out complete, etc.), several HIGH-cost (room vacated via raw SQL). This is what makes "rent overdue → collection task" or "move-out complete → deposit checklist" fire automatically. **It does NOT live in `submitTask` — it lives in invoice/refund/complaint/move-out services.** Calling it "one build" with 1a was inaccurate; it's a second workstream. Named, scoped, scheduled for the sprint after this cycle — not quietly deferred.

Together, 1a + 1b are the only capabilities that differentiate us from MaintainX, SafetyCulture, Xenia — every rival is a generic checklist; we are the only one that owns the property system and can write back into rent, deposit, complaints, KYC. Indivisible as an *outcome*; two builds as a *plan*. T9 names the failure mode if either half slips.

**User must-haves (ranked by user-miss; protect top, drop bottom):**

2. **Tasks visible inside the manager's daily app** — via the existing 23-card quick-filter registry the manager already trusts (categorized by tenant-status band: Current/Past/Bookings — see Audit §"Two pending-tasks systems"), not a new Flutter build. Task cards land in the existing taxonomy, deep-link to the webview for the full board.
3. **Due date, overdue state, reminders, escalation.** `task_instance` gets a `due_date`; `expired`/`overdue` get a writer; reminders fire on a cadence; escalation follows the complaint-v2 tier model (L1→L2→L3, Redis dedup for L2/L3 at `escalate_v2.ts:417-426`). The move-out checklist service has the WhatsApp *template names* for locked states and orphan reminders (`moveInMoveOutChecklistWhatsappService.ts:580-811` — includes `moveout_checklist_overdue` at :619 and admin-reminder at :644) but **no reminder cadence or escalation tier model is wired** — those are built from scratch, not a copy. The complaint-v2 escalation service is the real pattern to model on.
4. **A staff "my tasks today" view — and a staff-owned proof archive.** The staff member's own list, on their phone, in their language, with the one pending task they owe today. *And* their own proof archive they can pull up themselves: the 9am room-204 photo is in *their* hand at 4pm when the tenant complains, not only the owner's. This is the anti-surveillance move that makes the bet defensible — the tool belongs to the person doing the work, and the proof protects them.
5a. **Proof collected as part of the work.** Location (compared to property location — a real geofence, not dead-letter), time, signature where it matters (handover, cash, move-out).
5b. **Partial save / draft-resume.** Client-side IndexedDB autosave on field-blur, submit-when-online. Long tasks (move-out inspection, full-property audit) can take 20+ minutes in Indian summer heat (PM estimate, not validated); a network drop mid-form today means lost work the staff member has to redo, and the cost is borne by the lowest-paid person. Client-only draft is the v1 ship (FE-only, no schema, fits no-login-cheap-Android); server-side draft state is a fast-follow if telemetry shows real long-task abandonment. *Assumption to validate:* long-task abandonment rate — we have no analytics on this today.
5c. **Must-meet ship gate.** The runner cold-loads under 3 seconds on a 2G connection with no JS cache, and partial work survives a tab close / network drop / phone restart — or Need 2 doesn't ship. "Fast and offline-tolerant" without numbers is not a requirement.
6. **The review loop works end-to-end.** Approve, reject with reason, send-back-for-rework. The dormant columns get a writer; the disabled review controls come alive.
7. **Runner in Hindi + at least one regional language.** Priya reads Hindi better than English; Ramu needs Hindi / Telugu / Kannada. Launch requirement, not a fast-follow.
8. **A first insight cut (real, not token).** Three reads on the manager's task board (`manager.rentok.com/tasks`): **completion rate by staff member**, **failure rate by room**, **week-over-week trend** on both. Tap-through to the underlying instances (tap a failing room → see the failed submissions). No weighted scorecard, no custom report builder, no anomaly detection — just the shape of the problem so the manager stops reading 30 raw submissions to find the one bad room. *Denominators, numerators, and the dependency on 1a's `fail` state* (failure-by-room reads zero until 1a ships) live in the build sheet — owned there, not hidden.
9. **Shift handover between wardens/staff.** The night warden's job is half "what did the day warden flag for me?" — broken lights, sick tenants, keys in locker B, "skip 204 today." Today this happens on paper or WhatsApp, off-system. Ship a handover as a task-instance variant: outgoing shift writes it, incoming shift acknowledges. This is the spine of multi-shift ops (security, housekeeping, front desk) and the bet's warden personas depend on it. *Not* to be confused with the move-out eviction handover (`is_handover_complete` on `tenant_eviction_details`, gated by the `key_handover_access` / `otp_handover_recieve_access` permission flags) — different lifecycle, different entity, already covered by the existing pending-tasks registry.
10. **Manager mobile approval.** Priya at 2am gets a late-check-in request or a submission to approve. The manager-side path through the Flutter app (#2) gets us 80% there — adding approve/reject as a card action that deep-links to the webview and lands on the review page, not just the manager viewing. Mobile approve-from-a-card is universal in MaintainX/Linear/Asana; without it, the review loop (#6) is desktop-only and the bet's mobile-first manager is locked out of the highest-leverage action.
11. **Manager quick-capture (ad-hoc issue).** AC broke in room 204 — no checklist failed, it just broke. Today the manager has no way to log this from the Task module; it goes to a separate complaints tool or gets forgotten. Ship a quick-capture: manager logs an issue from the home card or the task board, scoped to room/entity, optionally auto-creates a corrective task. Every ops tool has this. Without it the module only handles checklist-originated work, which is half the actual ops volume.
12. **Skip + reschedule single instance mid-route.** Housekeeping mid-route finds room 204 tenant sick in bed — skip it, come back. Today the instance sits pending until overdue. Ship per-instance skip/defer/retry as a runner action (and a manager action). Universal in field-ops tools; without it housekeeping can't gracefully handle the real friction of the route.

**Differentiators (India-specific wins no rival matches; lean yes):**

13. **WhatsApp weekly digest for owners.** Owners in India live on WhatsApp. Monday morning: "Last week: 84% cleaning completion, 3 failed checks (all in Block B stairwell), 1 inspection overdue. Tap to see detail." (Sample copy, not live data.) Low effort — rolls the insight reads (#8) into a WhatsApp template dispatched via the existing `adminMetaHelper.sendOnlyBodyMessage` infra (`taskScheduler.ts:203-216` shows the precedent). The owner is a real persona for this module and is currently invisible to it; this is the lowest-effort way to make the module matter to owners every week.
14. **Audit export (CSV/Excel of task_instance rows).** FSSAI audit time: owner needs 6 months of fire-extinguisher checks for one property. Today they manually scroll. A filtered CSV export reuses data that already exists (no new schema, no new endpoints beyond a `/tasks/export` route over the existing `getTaskInstances` query at `taskController.ts`) and unlocks the compliance-purchase reason properties adopt the module. Defer the custom report builder (see not-build list) — just the filtered export.

**Team and strategic prereqs (not user-miss items):**

15. **Access control retrofitted across the module.** Four new task flags + inline `checkAuthInDb` on every existing endpoint. **Migration rule:** existing team members default to `view_tasks=true` on ship day (the gate is real for new assignments and role changes, not a Big Bang that locks out 64 currently-working managers). Without this, approve-tasks ship into a module with no permission spine.
16. **The redesigned web app ships as the baseline.** v2's track board, review queue, and builder replace the shipped version. Team-served call (managers get the better experience, we stop designing against two codebases), not a user-miss.
17. **Three security orphans closed.** Authenticate `/tasks/trigger` with a `cron_key`; add an admin guard to `TEMPLATE_LIBRARY` edits; the access-control retrofit (item 15) covers the third.
18. **Wake the two scope orphans.** `by_floor` and `manual` scope branches exist in the scheduler (`taskScheduler.ts:121-135`) but the controller (`createTaskSchedule` at `taskController.ts:73`) never accepts scope fields — its destructure list is `{ template_id, property_id, team_member_ids, frequency, send_time, pg_id }`, no scope_type/scope_value. One controller signature change unlocks both — high value relative to effort, currently absent from must-ship only because it's invisible, not because it's low-value.

## What we will not build this cycle

- **No built-in staff fines or salary deductions.** The owner owns the punishment; we own the proof. Bringing fines inside breaks the trust that makes the proof honest.
- **No weighted audit scorecard.** A weighted multi-question score with rollup is its own compute layer. Right thing after this cycle if structured-manager retention holds.[^scorecard] Need 3 (insight) *is* served this cycle by must-ship #8 — three specific reads with defined denominators and a tap-through to underlying instances. The full scorecard, custom report builder, anomaly detection, and predictive layers are out.
- **No staff attendance.** Operators in our sample solve attendance via a separate register; folding it into the proof tool muddies the bet (see #1 — proof, not surveillance). Defer until a user pain surfaces that the Task module is uniquely positioned to solve.
- **No AI generate-from-prompt for checklists.** The curated Library answers the blank-box problem better than generation for v1.
- **No re-build of move-out inspection inside the Task module.** The existing `tenant_checklist_item_move_out_meta` path already writes deductions and raises complaints. We surface it as a task entry on top; we do not fork.
- **No true multi-entity schema (one task, many entity TYPES).** `run_id` fan-out solves the same-type case (cleaning N rooms). The heterogeneous case (handover = tenant+room+keys+deposit) needs a new junction table cloned from `complaint_invoices_map`; build it the day a true-multi scenario actually ships, not before.
- **No building or floor as a task target.** A floor is not an entity with an ID; a building does not exist separate from a property. Floor-level work fans out to rooms via the existing by-floor scope (which itself needs the controller fix to become reachable — that's an orphan wake-up, not new build).
- **No native Tasks tab in Flutter.** Webview-via-deeplink from the homepage card covers Need 1 without a multi-week native build.
- **No QR/NFC checkpoint rounds this cycle.** Real feature for Ramu's security rounds (SafetyCulture/iAuditor built their business on it), but it depends on QR printing infra and a different field-type. Defer to the cycle after this one, scoped to security-rounds templates only — not every template. *Decision rule:* pull QR/NFC forward iff Round 1 surfaces Ramu as the primary persona; otherwise next cycle.
- **No staff self-claim from an open queue.** Real but conflicts with E3 (manager assigns, staff confirms). Defer until the assignment model is proven at launch.
- **No portfolio benchmarking (compare completion across properties).** Real for multi-property owners; depends on the multi-property aggregation rules in the registry. Defer — late-stage feature, the aggregation infra supports it later without rework.

[^scorecard]: Decision E1 in the Checklist Library project — deferred as fast-follow.

## Traps

**T1 — Watch-tool backlash (USER, launch-blocking).** Priya reads the new proof features (geo, signature, fail-to-action) as a tighter leash, and stops assigning tasks. Highest-magnitude risk. Mitigation is the staff-owned proof archive (must-ship #4) + manager-sees-exceptions-only + no fines inside the tool. *Launch test:* A/B the framing on staff completion rate is a weak instrument for a population documented to "quietly sabotage" — sabotage won't show as a completion-rate delta in a 2-week window. The real test is qualitative: post-launch staff interviews at week 4 asking "do you trust this tool?" Run both.

**T2 — The blank-box returns inside setup (USER).** If setup lists the library but doesn't *recommend* — by property type, by what managers like them run — we re-ship the flat sample grid with better paint. The Library's recommendation engine must land with this redesign.

**T3 — Building on the wrong codebase (TEAM).** v2 is not shipped. Designing against v1 throws the work away. v2 is the baseline.

**T4 — Forking the move-out deduction path (TEAM).** Two parallel inspection systems already exist. Building a task-native move-out that writes its own deductions forks financial logic. Surface, don't rebuild. Open question for later: whether `task_instance` and `tenant_checklist_item` should eventually unify — not this cycle.

**T5 — Geo captured but not shown (USER).** Collecting location on submit but never showing it in the proof view adds friction for no payoff and feeds the surveillance read. Location must show in the proof view, framed as confirmation, honest about cheap-phone GPS accuracy. And it must be a real geofence (compared to property location), not the dead-letter columns we have today.

**T6 — Shared-token attribution (USER).** When a schedule assigns several staff to one shared task, attribution is wrong, and for room cleaning — the most common task type — it is worst: the room-cleaning WhatsApp link carries `run_id` and `property_id` but **no per-person token and no team_member_id at all**, so every staff member who opens it submits against the same shared run. Any pattern view built on top inherits the wrongness. Per-person tokens for shared schedules, including the room-cleaning path.

**T7 — Half-gated permissions (TEAM).** Adding approve-task permission without retrofitting the existing task endpoints leaves create/edit/submit un-gated. The module becomes more inconsistent, not less. Item 15 in must-ship is non-negotiable.

**T8 — The runner doesn't load (USER, pre-mortem).** The whole bet rests on the runner being used, and its single most likely failure mode is page-load on a cheap Android in a basement. If we build all the proof features on a runner that doesn't load, we've added friction and lost the user. Ship gate is in must-ship #5: cold-load under 3s on 2G with no JS cache, or Need 2 doesn't ship.

**T9 — Differentiation collapse (STRATEGIC, the actual pre-mortem).** The moat (must-ship #1) is three capabilities sharing one hook-layer build. Under deadline pressure the team ships the redesign + cards + due-dates and quietly cuts fail→corrective, write-backs, and triggers *together* because they were never framed as one bet. The result: a polished staff checklist app with no integration, in a category that already exists. Mitigation is the restructure above — rank #1, fund #1, and if it slips the cycle slips.

## Build risks

- No task permission flags today (90 flags on `team_member_property`, none for tasks). Adding fits the existing `view_*/add_*/edit_*` pattern but the retrofit touches every existing endpoint.
- The status list has no fail/overdue/approved/rejected state — adding them is a DB enum change, manually deployed against a 160-script backlog under `src/scripts/` (no migration runner, no rollback).
- Three high-value entity targets (TenantEvictionDetail for handover, EntryExitRequest for gate exit, Inventory for asset inspection) are blocked on integer PKs. Prereq: widen `entity_id` from uuid to varchar (one schema decision, unlocks all three).
- No event bus codebase-wide. Event-driven tasks mean inserting hooks at each lifecycle site, not subscribing to a bus.
- Quiet hours / DND absent codebase-wide. If reminders are to respect night hours, that infrastructure has to be built from scratch.
- Two blocker eng questions open and gated at Nimit / Jatin (issues #1, #2 on `rentok-checklist-library`).
- The manager-side path through the Flutter app is a registry + deeplink change, not a redesign. But it is a prereq in its own worktree, and a hard dependency for shipping Need 1.

## Decisions locked

1. **Flutter entry point** — via the existing homepage card registry + deeplink-to-webview, not a native tab. Separate prereq, parallel, hard dependency for Need 1.
2. **v2 as baseline** — locked, recorded so it stops being relitigated.
3. **Round 1 order** — Runner first (Need 2, where trust is won or lost), then Track board, then Review queue.

## Changelog

- 2026-07-18 — drafted. Claims traced to the Persona Bible (verbatim quotes footnoted), the companion `Task Module - Feature Gap Audit.md` (15 domains × ~250 capabilities scored against code), the canonical pending-tasks registry in the vault, and direct codebase inspection. Process detail (multiple critique rounds, skill invocations, correction history) in session memory `project_task_module_redesign.md`.
- 2026-07-18 — doc-handoff-review pass (Reviewer 1 fact-check + Reviewer 2 readability, run in parallel). Fact fixes applied: 87→90 permission flags (verified by enumeration); "Twenty-two events were ranked" → "~22 candidate hook sites identified during grounding, bucketed by cost, preliminary — in this brief"; "no event bus anywhere" → "no domain-event bus (EventEmitter exists only for SSE streaming)"; WhatsApp citation 580-739 → 580-811 (acknowledges the orphan reminder templates). Readability fixes applied: changelog cut from 370 words to two lines; must-ship #5 split into 5a/5b/5c; must-ship #8 SQL jargon moved to build sheet pointer; "Surfacing" → "Showing up"; "Round 1 surface" → "Round 1 order"; "Do NOT copy" wall-of-text → bulleted; "surface/surfacing" private-dict cleanup; throat-clearing "This is a bet, not a certainty" cut; "watch-tool" repetition trimmed in T5. Verdict: fix-then-ship → ship. Body 4749→4190 words; slop CLEAN; footnotes clean; all load-bearing citations verified against code.
- 2026-07-20 — **User-day walk-through pass (user-flagged: "any other features & workflows from a user-first perspective?").** Capability taxonomy (15 domains × 250 items) was inventory, not a day-in-the-life. Walking real days for each persona found 7 gaps the capability lens missed. Added 6 to must-ship: shift handover (#9, the biggest gap — multi-shift ops spine), manager mobile approval (#10, mobile-first manager locked out of approve otherwise), manager quick-capture for ad-hoc issues (#11, today the module only handles checklist-originated work), skip+reschedule single instance (#12, housekeeping can't gracefully handle "tenant sick in bed"), WhatsApp weekly digest for owners (#13, India-specific win no rival matches), audit export (#14, unlocks the compliance-purchase reason). Three defers added to "not building": QR/NFC checkpoint rounds (real for Ramu's security but needs QR infra + new field-type; fast-follow if we lead with the security persona), staff self-claim queue (conflicts with E3), portfolio benchmarking (late-stage). Handover-vs-eviction-handover disambiguation noted inline (#9) — they share a word, not a lifecycle.
- 2026-07-20 — **Second doc-handoff-review pass on all 3 docs** (Brief + Audit + README, fact-check + readability + brief-skill mechanical audit). Found 2 CRITICALs + 8 MAJORs missed in the first pass; all applied. CRITICALs: Audit Domain 4 Rating score was hallucinated as 2 (backend enum has no rating → corrected to 0 with eng issue #5 ref); Brief had two conflicting 5-write-back lists (expense-recording vs fail→complaint — standardized on the fail→complaint outcome-framing that matches cited adapter functions). MAJORs: ~100→90 flags drift; getAllTenants.ts:2166→:2396 (wrong citation in Audit + flagged in Obsidian source); System B 12-card list (refund stub → joining_request_pending + refund-as-TODO); 23-card parenthetical (mixed taxonomies); "45 of 64 run 5+ staff" threshold sourced (Structured+Large tiers = 16+29); eviction-handover column/flag disambiguation (is_handover_complete column vs key_handover_access flag); "every claim traces to audit" overpromise narrowed (moat-build targets cite adapters directly); README template-count reconciliation (30/31/33 explained once); README PR #3/#6 siloing fixed (Start-here pointer lists both). Readability: surface/surfacing private-dict leaks purged (changelog had falsely claimed they were fixed); load-bearing, canonical, framework, taxonomy, matrix, derived, net-new all swapped; 4 sourceless numbers tagged "PM estimate, not validated"; 4 build-cost framings ("cheap/cheapest/cheap win") replaced with user-value framings or citations; "Worth a" hedge → decision rule; UI words screen/tab cleaned; Domain 4 wall-of-text cell trimmed; "How this brief was built" heading de-snarked; no-staff-attendance team-framing → user-pain framing. All fact fixes verified against codebase.
