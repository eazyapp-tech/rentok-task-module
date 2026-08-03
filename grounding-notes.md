---
title: "Task Module — Grounding Notes (what the code does today)"
date: 2026-07-21
owner: "Sanchay"
status: "current"
tags: [rentok, tasks, grounding, engineering]
---

# Grounding Notes — what the code actually does today

Findings from the 2026-07-21 grounding sweeps of `rentok-backend`. This is the engineer-facing companion to the [Feature Gap Audit](Task%20Module%20-%20Feature%20Gap%20Audit.md) — the Audit scores capabilities, this records the specific mechanics the design depends on. File:line references are point-in-time; verify before building.

---

## 1. The single most important architectural fact: there is no event bus

Every automation in the codebase is the **same shape — a timed cron that polls, evaluates a condition, and acts.** Nothing fires the instant a state changes.

| Mechanism | Trigger | Operator-configurable? |
|---|---|---|
| Complaint escalation (`complaintEscalationScript.ts`) | elapsed time vs per-property L1/L2/L3 TATs | Yes (per property + category) |
| Survey/review scheduler (`scheduleService.ts`) | frequency bucket matches today | Yes (frequency + cohort) |
| Task scheduler (`taskSchedule`) | `next_run_at <= now` && `is_active` | Yes |
| Dues / invoice crons | date-based (month rollover, due date) | Partly |

**Design consequence.** A **standing rule** is a natural extension of the existing task scheduler (frequency + scope + `next_run_at`) with a condition and a stop-condition added — poll-and-evaluate, the grain the whole codebase already runs on. Instant event hooks are the expensive, later thing. This is why entity-linked completion is a **read-time status check** (D3), not a push.

## 2. Room cleaning proves the thesis — "daily only" is a UI default, not a limit

`enableRoomCleaning` (`taskController.ts:467`) creates an ordinary `TaskTemplate` + `TaskSchedule` tagged `system_purpose:'room_cleaning'`, `scope_type:'all_rooms'`. **It is the generic task engine wearing a label**, not a separate mechanism.

- `frequency` is already an enum: `one_time | daily | weekly | monthly` (`taskSchedule.ts:40-45`).
- The room-cleaning path merely **defaults** it: `frequency: schedule_config?.frequency || "daily"` (`taskController.ts:515`); the update path already accepts a supplied frequency (`:493`).
- The scheduler already advances all four cadences (`taskScheduler.ts:226`).

**Design consequence.** Making the cadence operator-editable (F5, D16) is *surfacing a field*, not engine work. Only cadences the enum cannot express — every-N-days, specific weekdays — need an enum extension plus a `updateNextRunAt` branch. Folding room cleaning into the redesigned module is unification, not migration.

## 3. Filters: a fixed catalog of canned segments, not a composable filter engine

Announcements and survey campaigns share one mechanism: a single integer `filter_code` resolved by `filterTenantsFromFilterCode` (`helpers/announcements.ts:1943`); the review campaign calls the same helper (`campaignService.ts:71,110`).

Available segments include: all active tenants · has pending dues (and by due type: rent / electricity / others / mess / security deposit / unpaid late fine) · under notice · has active complaints · not activated · unsigned agreement · KYC docs missing · incomplete profile · joined 48h ago · by floor (single property) · explicit tenant list. Surveys add a **team-member** recipient axis and a **recurrence schedule**.

**Key negative finding:** there is **no room-type, sharing-type, room-tag, tenant-gender, or tenant-occupation facet** anywhere in this engine. Room geometry (`sharing_type`, `type`, `tags`) exists on the room table as static attributes but carries no occupancy signal and is not part of the filter catalog.

**Design consequence.** Standing rules should reuse the *product pattern* (a curated menu of meaningful segments — D7) rather than assume a composable rule engine exists to extend. Building a general filter builder is net-new work, and D7 rejects it anyway.

## 4. Trigger states: what is cheap to poll, and what is not

| State | Clean field? | Cost |
|---|---|---|
| Tenant status (active / upcoming / left) | Yes — `tenant.status`, indexed | Cheap |
| Under notice / move-out notice | Yes — `under_notice`, `date_of_eviction`, `notice_raised_on` | Cheap |
| Onboarding / activation | Yes — `onboarding_flag` | Cheap |
| Rent / dues overdue | No — computed from an invoice scan | Medium |
| **Room vacant / occupied** | **No — no `room.is_occupied`**; derived by joining room → beds → tenants | **Medium; the priciest** |

**Design consequence.** The flagship example ("clean every vacant room until it is filled") reads the *least* clean state in the system. If room-state standing rules are core, a persisted occupancy signal is a scoped prerequisite worth deciding before the build, not discovering mid-build.

## 5. Destination surfaces for linked entities

| Entity | Where it lives | Note for the design |
|---|---|---|
| **Complaint** | `addComplaint` (`controllers/complaints.ts:132`); `status` 0=Unresolved … 5=Resolved, 21=Pending Tenant Review; new complaints default `status=0` | Already accepts `inventory_id` and `tenant_checklist_item_id`, so a task→complaint link has a native path. Escalation is a separate cron that picks up any `status=0` complaint automatically. |
| **Invoice / due** | `invoices.status` — **0=Not Paid, 1=Paid, 2=Partially Paid, 3=Refunded, 4=Loss** + `paid_amount`, `paid_date` | "Paid" is a status plus amounts, never a boolean, and is only moved through the payment controllers. A read-time suggestion must fire only on **fully settled** — partial / refunded / waived must not suggest close. |
| **KYC** | `tenant.is_aadhar_verified`, set inside the tenant edit path after the Aadhaar fetch | There is no single "mark KYC done" mutation, and a task links to a **tenant**, not to a KYC state. |
| **Move-out deductions** | `markMoveOutItem` (`moveOutChecklistService.ts:359`) → `createInvoicesAndExpensesForOwner` (`:559`); append-only meta rows carrying condition + damage cost | **Already built and correct — reuse, do not rebuild** (D14). |
| **Deposit refund gate** | `property.is_refund_deposit_blocked_until_all_checklists_locked` | A read-time guard on refunding Security Deposit / Caution Money; writes nothing itself. |
| **Asset** | The **Inventory** entity — has `status` (AssetStatus) and `damage_cost`, **no `condition` column** | Physical condition is captured on the move-out checklist meta. An asset-condition task should use the existing fields or the checklist, not invent a column. |

## 6. The task submit path has no hook layer, and no auth

`submitTask` (`taskController.ts:210`) is a bare submit: load instance by token → guard double-submit → set `responses` (raw JSON, unvalidated) → `status = 'submitted'` → save → return. The route `POST /tasks/runner/:token/submit` has **no header validation, no permission check, and no post-save dispatch**. `TaskInstance.status` is `pending | submitted | expired`.

**Design consequence.** Grounds F36 (server-side validation of submissions) and F26 (access control; close the open routes). Any per-submission behavior is net-new logic at this seam.

## 7. Comments and @mentions: lift the convention, build the store

There is **no reusable comment component**. Complaint "comments" are `complaint_history` rows written by `AddComplaintRemarks.service`; the entity is a generic audit/history table, not a comment thread. A mention is a `@{uuid}` token embedded in the remark text, extracted by a regex parser, and the tagged person is notified **by email only** — the push path is dead code.

**Design consequence.** F14 can reuse the `@{uuid}` convention and the parser, but the task module needs its own comment store and a channel-agnostic notify step. Not the clean reuse the design first assumed.

---

## How to use this file

Cite it from workflow specs' Engineering Notes and from the PRD's open questions. It records *what is true today* — when a claim here is acted on or invalidated, update it and say so, rather than letting a stale line be treated as ground truth.
