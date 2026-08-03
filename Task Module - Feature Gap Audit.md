---
title: Task Module — Feature Gap Audit
date: 2026-07-18
tags: [rentok, audit, tasks, staff, module-redesign]
owner: Sanchay
status: companion-to-brief
---

# Task Module — Feature Gap Audit

Companion to `Task Module Brief.md`. This is the systematic feature-space audit the brief rests on: every capability a top-1% ops-task product needs, scored against what the code actually has today. Read this when questioning *why* the brief's must-ship list is what it is, or when a future "did we miss X?" question comes up.

## How to read

Three competitive baseline tiers:
- **[TS] Table stakes** — every serious competitor (MaintainX, SafetyCulture, Xenia, Lumiform, GoAudits, Fiix, UpKeep, Connecteam, Yardi, Cribb) has this. Absence = below floor.
- **[D] Differentiator** — only the best have it; building it moves us up the stack.
- **[I] Innovation** — rare or emerging; where we can leapfrog. Not this cycle unless noted.

Score per capability:
- **0** absent · **1** partial/hacky · **2** functional but behind · **3** best-in-class

The redesign must lift every **[TS] item at 0 or 1** to at least 2. **[D] items at 0** are the roadmap. **[I]** items are out of scope unless they fall out of the bet cheaply.

---

## Domain 1 — Task types & kinds

| Capability | Tier | Score | Code reality |
|---|---|---|---|
| One-off / ad-hoc task | TS | 1 | `frequency='one_time'` exists (`taskSchedule.ts:42`); no API to create one without a schedule |
| Recurring task | TS | 2 | daily/weekly/monthly work (`taskScheduler.ts:230`) |
| Checklist | TS | 2 | the only kind today |
| Approval / gate task | D | 0 | dormant columns only (`taskInstance.ts:82-89`); no `approved`/`rejected` status |
| Corrective action / CAPA | D | 0 | no fail state, no auto-spawn |
| Inspection (scored) | D | 0 | no scoring |
| Data-collection / survey | D | 0 | structurally possible, never used |
| Request / ticket (tenant- or staff-initiated) | D | 0 | no inbound path |
| Work order (cost/parts/vendor) | D | 0 | no schema |
| Preventive maintenance (asset-linked) | D | 0 | no asset link |
| Sign-off / acknowledgement | D | 0 | no sign-off type |
| Move-in/out inspection | D | 1 | exists as a PARALLEL system (`tenant_checklist_*`), not wired to task module |

**Gap verdict:** only 2 of ~11 task kinds work. The module is a single-kind product.

---

## Domain 2 — Triggering & scheduling

| Capability | Tier | Score | Code reality |
|---|---|---|---|
| One-time at fixed date+time | TS | 1 | supported in engine, not exposed via API |
| Recurring daily/weekly/monthly | TS | 2 | works |
| Custom cron (Tue+Thu, 2nd Friday) | D | 0 | no custom cron |
| Relative-to-anchor (X days after move-in) | D | 0 | no anchor logic |
| Blackout / regional holidays | D | 0 | none |
| Time-window execution (9–11am) | D | 0 | `send_time` exists, no window |
| Multi-property stagger | D | 0 | none |
| Status-change trigger (event-driven) | TS | 0 | no event bus; status flips are inline |
| Record-creation trigger | TS | 0 | none |
| Threshold trigger (rent unpaid 3d) | D | 0 | none in task module (late-fine cron exists separately) |
| Failure→corrective trigger | TS | 0 | no fail state |
| Rule-based conditional spawn | D | 0 | none |
| Manual assignment | TS | 2 | works |
| Bulk create | TS | 1 | partial |
| Quick-add from home | D | 0 | none |
| AI-suggested (history-based) | I | 0 | out of scope this cycle |
| `by_floor` scope | TS | **0 (orphan)** | code present, unreachable — `createTaskSchedule` doesn't accept scope fields |
| `manual` room-subset scope | TS | **0 (orphan)** | same — unreachable |
| `next_run_at` catch-up | TS | 2 | works (`taskScheduler.ts:226`) |

**Gap verdict:** scheduled tasks work; event-driven, rule-based, and 2 of 4 scope branches are absent or orphaned.

---

## Domain 3 — Assignment & access control

| Capability | Tier | Score | Code reality |
|---|---|---|---|
| Single assignee | TS | 2 | `team_member_id` |
| Multi-assignee (per-member instances) | TS | 2 | works (`taskScheduler.ts:152-167`) |
| Property-scoped | TS | 2 | works |
| Reassignment | TS | 1 | soft-deactivate + re-add; no true reassign |
| Role-based access control | TS | **0** | no task permission flags; task controller has zero `checkAuthInDb` calls |
| Granular perms (view/edit/complete/approve/delete) | TS | **0** | none |
| View-assigned-to-me vs view-all | TS | **0** | no `view_assigned_tasks` equivalent |
| Delegation | D | 0 | none |
| Coverage / substitution rules | D | 0 | none |
| Out-of-office routing | D | 0 | none |
| Capacity / load balancing | D | 0 | none |
| Vendor / contractor external assignee | D | 0 | no external-user concept |
| Approval-only role | D | 0 | no approve permission exists |
| Portfolio scoping (regional mgr) | D | 1 | `pg_id` scoping only |
| `reactivateTeamMember` repo method | TS | **0 (orphan)** | defined, never called by any route |

**Gap verdict:** assignment works; access control is the largest 0 in the module. Introducing approve-tasks adds the first permission gate to a module that has none — must retrofit existing endpoints too, or ship a half-gated inconsistent UX.

---

## Domain 4 — Execution & proof

| Capability | Tier | Score | Code reality |
|---|---|---|---|
| Text / number / yes-no / photo / dropdown fields | TS | 2 | works (`taskTemplate.ts:27`) |
| Rating (5 / 10) | TS | **0** | backend enum at `taskTemplate.ts:27` has no rating type (`text`/`number`/`yes_no`/`photo`/`select` only); Checklist Library templates use `rating_5`/`rating_10` that the backend cannot represent. Eng issue #5 confirms rating-threshold support is unproven. |
| Signature | TS | **0** | doesn't exist anywhere |
| Barcode/QR scan | D | 0 | none |
| NFC tap | I | 0 | out of scope |
| Conditional field (show only if) | D | 0 | FE-only convention; no BE support |
| Section / repeatable section | D | 0 | none |
| Calculation / formula | D | 0 | none |
| Required / optional | TS | 2 | works |
| Mandatory photo on failure | D | 1 | `image_config.required_on_values` exists as FE convention; NOT IN BACKEND CODE |
| `critical` + `fail_on` field conventions | D | **0 (convention-only)** | in Checklist Library templates; not in code |
| Offline mode | TS | **0** | no PWA, no offline tolerance — runner is online-only Next.js |
| Partial save / resume | D | **v1 ship (Direction A)** | client-side IndexedDB autosave, submit-when-online. FE-only, no schema. Server-side draft deferred unless telemetry shows abandonment. See Brief §5b. |
| Draft auto-save | D | **v1 ship** (paired) | localStorage in v2 builder is the precedent; extend to runner with IndexedDB for larger photo payloads |
| Geo-fence (must be at location) | D | **0 (orphan)** | COMP-025 spec exists; coords stored; never compared to property location |
| Geo check-in/out | D | 0 | none |
| Auto timestamp | TS | 2 | works |
| Device attestation / anti-spoof | I | 0 | out of scope |
| Multilingual UI + field labels | TS | **1** | runner is English-only; personas need Hindi/Telugu/Kannada |
| Voice-to-text | I | 0 | out of scope |
| Pre-filled answers from linked entity | D | 0 | none |
| Idempotency on submit | TS | 2 | works (`taskController.ts:220`) |
| `responses` jsonb validation | TS | **0** | FE posts arbitrary JSON; no validator against `template.structure` |
| Runner page-load / low-bandwidth | TS | **0** | React hydration on cheap Android in basement = abandonment |

**Gap verdict:** field types are OK; proof (signature, real geo, offline) and access (validation, language) are the deep gaps. Runner load-time is the single biggest staff-side risk.

---

## Domain 5 — Review & workflow

| Capability | Tier | Score | Code reality |
|---|---|---|---|
| Approve / reject | TS | **0** | dormant columns, zero writers |
| Return for rework with reason | TS | **0** | dormant |
| Reviewer comments | D | 0 | none |
| Rejection reason capture | TS | **0** | column exists (`rejection_reason`); no writer |
| Multi-step approval chain | D | 0 | none |
| Auto-approve rules (score≥95% etc) | D | 0 | no scoring |
| SLA on review | D | 0 | none |
| Review-batch (bulk approve) | D | 0 | none |
| Conditional review (flagged-only) | D | 0 | none |

**Gap verdict:** review is 100% dormant. Some schema is in place — dormant columns just need a writer + enum values (`approved`/`rejected`, plus `fail` per Domain 6). Move-out checklist is the template (separate submit/approve flags, approve-is-superset-of-submit at `moveInMoveOutChecklistController.ts:260-270`).

---

## Domain 6 — Fail & action engine

| Capability | Tier | Score | Code reality |
|---|---|---|---|
| Auto-create corrective task on fail | TS | **0** | no fail state in enum |
| Auto-raise issue/complaint | D | **0** | pattern exists elsewhere (`moveOutChecklistService.ts:657`); not wired to task module |
| Severity routing | D | 0 | no severity |
| Linked parent/child corrective tasks | D | 0 | no lineage |
| Re-check loop | D | 0 | none |
| Escalation on repeated failure | D | 0 | none |
| Trend detection | D | 0 | none |
| Auto-notify on fail | TS | 0 | none |
| CAPA workflow | D | 0 | none |

**Gap verdict:** entirely absent. This is the MaintainX competitive pattern and the biggest single capability gap vs rivals.

---

## Domain 7 — Due dates, reminders, escalation

| Capability | Tier | Score | Code reality |
|---|---|---|---|
| Due date + time | TS | **0** | `task_instance` has NO `due_date` column |
| Overdue state | TS | **0** | enum has `expired`, no job sets it; no `overdue` concept |
| Reminder cadence (pre/due/post) | TS | **0** | one-shot WhatsApp at trigger; no follow-up |
| Escalation tiers | D | **0 (but strong precedent)** | complaint `escalate_v2.ts` is the tier template (L1/L2/L3, Redis dedup, time-based) |
| Quiet hours | D | **0 (codebase-wide)** | no DND concept anywhere |
| Snooze / defer | TS | 0 | none |
| Auto-close | D | 0 | none |
| Auto-reopen | D | 0 | none |
| "Still pending" digest | D | 0 | none |
| Daily-plan morning brief | D | 0 | none |
| Pre-due warnings | D | 0 | none |
| WhatsApp-first brief delivery | D | 0 | template exists, no brief job |
| Move-out checklist reminder/overdue templates | D | **0 (orphan)** | templates BUILT at `moveInMoveOutChecklistWhatsappService.ts:607-811`, never wired |

**Gap verdict:** no time pressure exists on tasks today. The orphan move-out WhatsApp set is the gift — copy it.

---

## Domain 8 — Entity linking & context

| Capability | Tier | Score | Code reality |
|---|---|---|---|
| Polymorphic link (entity_id + entity_type) | TS | 2 | works (`taskInstance.ts:37-41`) |
| Tenant link | TS | 1 | structurally possible, never used |
| Room link | TS | 2 | works |
| Property link | TS | 2 | works |
| Invoice (dues) link | D | 0 | never used |
| Complaint link | D | 0 | never used (but FKs pre-exist on complaints) |
| Team member link | TS | 2 | works (assignee) |
| KYC link | D | 0 | never used |
| Asset / inventory link | D | **0 (blocked)** | `inventory` has int PK — needs `entity_id` widened to varchar |
| Eviction/handover link | D | **0 (blocked)** | `tenant_eviction_details` int PK — same |
| Move-out checklist link | D | 1 | parallel system (`tenant_checklist_*`); not unified |
| Multi-entity (one task, many entities of same type) | TS | 2 | via `run_id` fan-out |
| Multi-entity (one task, many entity TYPES) | D | **0** | needs new `task_instance_entity_map` junction table |
| Parent/child task lineage | D | 0 | none |
| Pre-filled answers from entity context | D | 0 | none |
| QR/NFC auto-detect of asset | I | 0 | out of scope |

**Gap verdict:** the polymorphic engine is the superpower, under-used. 5 write-back paths are *designed* with precedent patterns to copy (`markMoveOutItem` at `moveOutChecklistService.ts:359`, complaint auto-raise at `:657`) — but the hook layer on `submitTask` does not exist today and must be built from scratch. Calling these "READY" was inaccurate; they are designed-and-precedented, not built. 3 high-value targets blocked on int-PK schema decision.

---

## Domain 9 — Templates & content

| Capability | Tier | Score | Code reality |
|---|---|---|---|
| Template library | TS | 1 | `TEMPLATE_LIBRARY` rows exist; row count unknown from code |
| Categorization | TS | 0 | none |
| Recommendation by property type | D | 0 | none (Checklist Library project is building this) |
| Versioning | D | 0 | none |
| Branching / forking (per-property override) | D | 0 | none |
| Sharing across properties/portfolio | D | 0 | none |
| Template analytics | D | 0 | none |
| AI generation | I | 0 | out of scope this cycle |
| Import/export | TS | 1 | client-side report export only |
| Multi-language templates | TS | 0 | none |
| Template permissions (who can edit) | TS | **0 (security gap)** | any authenticated user can edit `TEMPLATE_LIBRARY` rows (`taskController.ts:726`) |
| Template inheritance | D | 0 | none |
| Marketplace | D | 0 | none |

**Gap verdict:** library is bare. Checklist Library project covers content + recommendation; template-permissions security gap is real and unfiled.

---

## Domain 10 — Insights & reporting

| Capability | Tier | Score | Code reality |
|---|---|---|---|
| Completion rate | TS | 0 | none |
| On-time rate | TS | 0 | none (no due date) |
| Failure rate by question | D | 0 | none (no fail state) |
| Failure rate by asset/room/person | D | 0 | none |
| Trend | D | 0 | none |
| Scorecards | D | 0 | none (E1 deferred) |
| Audit trail / log | TS | 0 | none |
| Export | TS | 1 | client-side only |
| Scheduled reports | D | 0 | none |
| Heatmaps | D | 0 | none |
| Drill-down | D | 0 | none |
| Filters | TS | 1 | basic property filter only |

**Gap verdict:** no insight layer whatsoever. Today the manager has raw submissions; the shape of the problem is invisible.

---

## Domain 11 — Notifications & comms

| Capability | Tier | Score | Code reality |
|---|---|---|---|
| WhatsApp channel | TS | 2 | works (`task_checklist` template) |
| Push notifications | TS | 0 | none |
| In-app inbox | TS | 0 | none (staff have no inbox) |
| Email | TS | 0 | none |
| Per-event vs digest | D | 0 | one-shot only |
| Per-role preferences | D | 0 | none |
| Channel fallback | D | 0 | none |
| Action-from-notification | D | 0 | none |
| Read receipts | D | 0 | none |
| Quiet hours | D | 0 | codebase-wide absence |
| Multi-language notifications | TS | 0 | none |
| Smart batching | I | 0 | out of scope |

**Gap verdict:** single-shot WhatsApp only. No push, no inbox, no escalation channel. The runner's only lifeline is one message the staff member must not lose.

---

## Domain 12 — Integration & automation

| Capability | Tier | Score | Code reality |
|---|---|---|---|
| Webhooks out | D | 0 | none |
| Triggers to other modules (fail→complaint, etc) | D | 0 | none (but 5 write-back paths designed with precedent patterns to copy — `markMoveOutItem` + complaint auto-raise in moveOutChecklistService.ts) |
| REST API | TS | 2 | exists |
| Inbound webhooks | D | 0 | none |
| Rule engine (no-code automation) | D | 0 | none |
| Native comms integrations | TS | 1 | WhatsApp only |
| Calendar sync | D | 0 | none |
| Event bus / pub-sub | I | 0 | out of scope (no event bus codebase-wide) |

**Gap verdict:** integration is where we can uniquely win because we own the property system. Rivals can't write to rent invoices or move-out deductions; we can. The 5 designed write-back paths (with code precedent to copy from `moveOutChecklistService.ts`) are the competitive moat — but the hook layer to actually wire them is built from scratch, not "READY."

---

## Domain 13 — Audit, compliance, evidence

| Capability | Tier | Score | Code reality |
|---|---|---|---|
| Lock-after-submit | TS | 1 | idempotency rejects re-submit, but no edit lock |
| Version history | D | 0 | none |
| Audit log of edits | TS | 0 | none |
| E-signatures | D | 0 | none |
| Watermarking (user/timestamp on photos) | D | 0 | none |
| Tamper-evident photos | I | 0 | out of scope |
| Compliance framework templates (FSSAI/NBC) | D | 1 | Checklist Library has some |
| Export-for-audit | D | 0 | none |
| Retention policies | D | 0 | none |

**Gap verdict:** thin. Audit-log-of-edits is a table-stakes gap for a tool that produces proof.

---

## Domain 14 — Mobile / PWA excellence

| Capability | Tier | Score | Code reality |
|---|---|---|---|
| Offline-first | TS | **0** | runner is online-only Next.js |
| Background sync | D | 0 | none |
| Photo compression before upload | TS | 0 | none |
| Low-bandwidth mode | TS | 0 | none |
| Deep-linking from WhatsApp | TS | 2 | works (token URL) |
| Installable PWA | D | 0 | none |
| Kiosk mode (shared device) | D | 0 | none |
| Shared-device quick-switch | D | 0 | none |
| Low-end Android (Android Go, <2GB) | TS | **0** | unverified; React hydration risk |
| SMS fallback | I | 0 | out of scope |

**Gap verdict:** runner is the trust-critical surface and it's the least-optimized for the actual deployment context (cheap Android, low signal, shared device).

---

## Domain 15 — Admin & ops of the module itself

| Capability | Tier | Score | Code reality |
|---|---|---|
| Bulk operations | TS | 1 | partial |
| Archive / restore | TS | 0 | hard delete only |
| Audit log of task edits | TS | 0 | none |
| Ownership transfer (manager leaves) | D | 0 | none |
| Staff offboarding (reassign open tasks) | D | 0 | none |
| Migration tools (from Excel/paper) | D | 0 | none |
| Sandbox / test mode | D | 0 | none |
| Feature flags | D | 0 | none |
| Module health monitoring | I | 0 | none |
| `/tasks/trigger` cron authentication | TS | **0 (security gap)** | no `cron_key` at `taskRoutes.ts:60`; anyone with URL can spam instance creation |

**Gap verdict:** admin layer is thin. The unauthenticated trigger route is a security orphan.

---

## Two pending-task systems in the app — disambiguation (read this before extending either)

The phrase "pending tasks" means two different things in this codebase. Both matter for this redesign.

**System A — The quick-filter feed (LIVE in Flutter today, 23 cards).** Source: `getAllTenants.ts:2396` (`quickFilter` method) → `/property/:id/quick_filter` route → `tenantPendingTasks` Flutter API. Registry documented at `RentOk Manager/Pending-Tasks-Reference.md` in the Obsidian vault (which itself mis-cites the line as `:2166` — `:2396` is correct). Twenty-three cards across three tenant-status bands:

- **Current tenants (status=1), 15 cards (A1–A15):** rent overdue, eviction requests, eviction extensions, keys to collect, agreement renewals, KYC pending, sign agreement, rate departing, police verification, move-in checklist pending, autopay setup, renewed-agreement-not-signed, install app, move-in checklist not locked, move-out checklist not locked.
- **Past/evicted tenants (status=0), 2 cards (B1–B2):** final dues to collect, deposits to refund.
- **Bookings (status=2), 6 cards (C1–C6):** booking requests, move-ins this week, token payments, bookings without room, bookings KYC, bookings sign agreement.

Each card has: a stable `filter_code` (used for deep-linking), a category (People / Money / Compliance / Operations), a priority color (red / orange / blue / green — though newer mobile builds flatten to `#3757FE`), an icon, and a feature gate where relevant (`complete_handover_enabled`, `movein_moveout_checklist_enabled`). Cards are hidden when count = 0.

**System B — The v1 homepage analytics block (built in backend, 12 defined cards, 10 active + 2 commented, NOT yet rendered in Flutter).** Source: `GET /v1/home/analytics/pending-tasks` → `src/v1/homepage/service.ts`. Twelve defined cards across Money / People / Property / Daily Ops (payments_not_linked, joining_request_pending, move_in_checklist_pending, move_out_request_pending, agreement_renewals_overdue, unassigned_complaints, inspection_pending, app_update_available, food_menu_review, expense_review, tutorial_available, and a `refund_request_pending` stub that is TODO-not-implemented). Backed by the `PendingTaskDismissService` Redis TTL system.

**The unified registry (the source of truth, supersedes A and B).** `RentOk/Product/pending-tasks-registry.md` in the vault reconciles A + B + the PRD spec + three rounds of codebase entity analysis into one product-and-engineering contract: **65 entries (64 active + 1 deprecated)** across 7 categories (Money, People, Compliance, Property, Daily Ops, Growth, Platform). It carries the structure the redesign must extend, not invent:

- **T1–T5 priority tiers** with explicit tier definitions and **dynamic tier promotion** rules (unassigned complaint > 24h promotes T2→T1; salary > 7 days overdue promotes T2→T1; agreement overdue > 30 days promotes T3→T2; etc.).
- **Source-type categories**: System-detected / Time-triggered / Event-driven. This is the trigger system.
- **Permission-to-task table**: every task names its `Required Access` flags from `team_member_property` with `ANY`/`ALL` logic. Owner/Admin see all; team members see by flag; feature gates apply independently.
- **Multi-property aggregation** (`sum`/`max`/`any`), role-filtered aggregation, "from N properties" attribution.
- **Assignee visibility** (admin view): computed at read time from `team_member_property` — "Sanchay + 2 assigned" chip.
- **Implementation-status flag** on every card: Live (new system) / Live (old system) / New / Deprecated.

**The proactive-suggestion layer (T6).** `RentOk/Product/T6 Proactive Tasks — v2 Candidates.md` specs **48 additional proactive candidates** beyond the 37 T6 tasks already in the registry, across three layers: Feature Discovery (configure payout, enable GST, mask phone numbers, auto-adjust late fine), Data Completeness (missing bank account, missing GST number, no payout mode), Workflow Intelligence. If added, the registry grows from 102 to 147 entries. This is the rule-based engine — designed and pending review.

**Implication for the redesign — selective, not blind.** The Task module is a different beast from the manager-nudge feed: staff physically complete checklists, vs the registry's cards that count things from other tables (rent overdue, KYC, agreements). What translates: category chips + priority colors, `filter_code` deep-link router, dismiss service, multi-property aggregation, the permission-table *concept*. What does NOT translate cleanly: the T1–T5 tier *definitions* (money-shaped, need ops-shaped equivalents for tasks — "proof gap for a tenant complaint in flight", "compliance deadline today"); the source-type categories (most task cards are `task_instance` rows past `due_date`, not state computed from other tables); the assignee-via-permission-computation (task assignment is explicit via `task_schedule_team_member`, not computed at read time). The T6 proactive layer doesn't apply at all — it's feature-config discovery ("enable payout"), a different mental model from staff-work completion.

The Task module adds **four new cards** to the existing Property category (Cleaning Overdue, Review Queue, Failed Checks This Week, Staff Tasks Overdue), using the registry's visual vocabulary and infrastructure but with task-specific mechanics. The staff-side "my tasks today" worklist is a separate surface with a different audience and action — not a pending-tasks card at all.

**Open fast-follow question (not blocking this redesign):** the T6 proactive layer is feature-config discovery for the *manager*. The Checklist Library has its own recommendation engine for *template adoption*. The question is whether the Task module needs a third, ops-shaped adoption-suggestion surface — e.g. "you run a 50-bed PG and have no daily room-cleaning checklist; adopt one." This may belong to the Library's engine (likely), or it may be distinct enough to warrant its own T6-style layer scoped to the Task module. Decide when the Library's recommendation engine ships and we see where adoption stalls.



---

## Cross-cutting: RentOk-specific strengths to lean into

These don't fit a domain but shape every decision:

- **WhatsApp is the #1 channel in India.** Treat it as first-class, not bolt-on.
- **Multilingual depth is survival** — Hindi + at least 4 regional languages, in UI AND content.
- **High staff churn** — onboarding a new housekeeper must be <5 min; offboarding must auto-reassign.
- ~~**Shared devices** — kiosk/quick-switch is the default deployment pattern, not a corner case.~~ **Corrected 2026-08-03 (D74): this is not true of RentOk's customer base — staff have their own numbers.** Kiosk and quick-switch stay in the v2 backlog as a watch item, not a known gap. Domain 14's two shared-device rows should be read as [D]-tier future work rather than deployment reality. The low-bandwidth findings below are unaffected and stand.
- **Compliance surface is wider than Western peers** — FSSAI, NBC fire, police verification, municipal licenses, GST.
- **Low bandwidth + low-end Android** — 2G/3G, Android Go. Photo compression and offline are survival.

---

## Three biggest orphans (built but never wired)

1. **Move-out checklist reminder/overdue/auto-locked WhatsApp service** (`moveInMoveOutChecklistWhatsappService.ts:607-811`) — the full reminder/overdue/escalation template set the task module needs, already built for a sibling system. Copy the pattern, don't invent it.
2. **`inspection_pending` homepage card** (`v1/homepage/service.ts:1624-1635`) — commented out; the bridge from the Task module to the Flutter-rendered homepage feed. Dismiss-map entry still wired. Wake it up and tasks appear in the manager's app.
3. **`by_floor` + `manual` scope branches** (`taskScheduler.ts:121-135`) — fully implemented, unreachable because `createTaskSchedule` doesn't accept scope fields. One controller change unlocks both.

## Three biggest security orphans

1. **`/tasks/trigger` is unauthenticated** (`taskRoutes.ts:60`) — no `cron_key`; single point of failure.
2. **`TEMPLATE_LIBRARY` edit has no admin guard** (`taskController.ts:726`) — any authenticated user from any org can mutate shared library templates.
3. **Task controller has zero `checkAuthInDb` calls** — every endpoint is gated only by JWT validity, not by property permission.

## Companion to
- `Task Module Brief.md` — the WHY, the bet, the cuts, the traps (the ~1500-word argument).
- This doc — the WHAT and the gap scoring (the evidence the argument rests on).
- Checklist Library repo — the in-flight content + recommendation workstream for templates.

---

## Changelog

- 2026-07-18 — **Self-critique correction.** "READY today" claims about the 5 write-back paths were inaccurate. `submitTask` (`taskController.ts:210-239`) only writes `status` + `responses` + lat/long — there is no hook layer, no dispatcher, no reusable write-back function. The precedent *pattern* exists in `markMoveOutItem` (`moveOutChecklistService.ts:359`) and the inline helpers (`createInvoicesAndExpensesForOwner:559`, complaint auto-raise:657), but a pattern to copy is not built infrastructure. Corrected to "designed with precedent to copy from" in Domain 8 verdict, Domain 12 row, and Domain 12 verdict.

---

## Changelog

- 2026-07-20 — **Second doc-handoff-review pass.** Domain 4 Rating score corrected 2→0 (backend enum at `taskTemplate.ts:27` has no rating; eng issue #5 confirms). System A citation `getAllTenants.ts:2166` → `:2396` (actual `quickFilter` method); Obsidian source `RentOk Manager/Pending-Tasks-Reference.md` flagged as carrying the same error. System B card list corrected: `refund_request_pending` is a TODO stub, active card is `joining_request_pending`; "12 cards" qualified as 10 active + 2 commented. Domain 5 verdict "~80% pre-staged" → "some schema in place"; enum-additions count expanded to include `fail` (not just approved/rejected). Domain 15 row gained `taskRoutes.ts:60` citation. Disambiguation section: private-dict leaks purged (load-bearing → read-this-first; canonical → the-unified-registry; framework/taxonomy/matrix → tiers/categories/table; derived → computed-from-other-tables; net-new → four new). Domain 4 partial-save cell trimmed (wall-of-text → one-line pointer to Brief §5b).
