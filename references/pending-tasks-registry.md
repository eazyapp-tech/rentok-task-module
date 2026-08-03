---
title: "Pending Tasks — Comprehensive Task Registry"
date: 2026-04-24
tags:
  - rentok
  - pending-tasks
  - product-spec
  - task-registry
  - prd
  - manager-app
aliases:
  - Task Registry
  - Pending Tasks Registry
---

> [!INFO] Source of Truth
> Local file: `docs/pending-tasks-registry.md` in the RentOk Backend repo. This Obsidian copy is synced from the local source.

# Pending Tasks — Comprehensive Task Registry

> [!WARNING] Verification note — 2026-07-21
> This is a **target spec written 2026-04-24**, not a description of current code. It was verified against `master` on 2026-07-21. What changed:
> - The pending-tasks feed was rewritten into a block-based v2 system. **All line numbers in this doc are stale.** The live method is `getPendingTasks()` in `src/v1/homepage/service.ts` (~line 1310), route `GET /v1/home/analytics/pending-tasks`.
> - **"No role filtering" is now FALSE** — per-property permission gating shipped (`resolveVisibilityContext` + `TeamMemberProperty`, gating on `can_view_*`). Ignore that gap.
> - **~10 live task types today** (not 11), all still hardcoded. `inspection_pending` and `move_in_request_pending` are commented-out dead code.
> - Still accurate: no task config table, no ranking, no assignee, no multi-property attribution, no top-N, no urgency text, no search. The permission-flag names are all real (~90 flags on `team_member_property`; the feed uses ~6).
> - Realizing the full 65-entry registry is tracked at eazyapp-tech/rentok-backend#6249.

This is the canonical, unified registry of every operational task that can surface in Pending Tasks. It reconciles three existing sources (new homepage system, old quick_filter system, PRD spec) and adds new tasks identified from three rounds of codebase entity analysis.

This document is the **target spec** — it specifies what tasks should exist, how they trigger, what their card looks like, who can see them, and how they sort. It is not a description of shipped code: today the tasks, their triggers, and their card content are **hardcoded**, and there is no ranking layer (see Structural Gaps at the end). Making trigger conditions, ranking, card content, and routing backend-configurable is the goal this spec sets, tracked at eazyapp-tech/rentok-backend#6249.

Onboarding tasks (bank not linked, rooms not added, etc.) are tracked separately and are out of scope here.

---

## How to Read This Document

| Field | What It Tells You |
| --- | --- |
| **Task Name** | Exact label shown in the app (some include dynamic counts or amounts) |
| **Source Type** | How the task is triggered — see Source Classification below |
| **Section** | Which category the task belongs to in View All |
| **Meaning** | What the count represents |
| **Operator Description** | Plain-English explanation a PG manager would understand |
| **Calculation Logic** | Exact formula / query condition from the backend |
| **Source** | Backend file:line reference or "New — not yet implemented" |
| **Visibility** | When this task appears (always shown if count > 0 unless noted) |
| **Priority Color** | Red = urgent action needed, Orange = attention needed, Blue = informational, Green = positive |
| **Priority Tier** | T1–T5 ranking within the category — see Priority Framework below |
| **Sort Signal** | What drives ordering within same tier — see Priority Framework below |
| **Promotes To** | Runtime condition that escalates the tier — see Priority Framework below |
| **Required Access** | Permission(s) from `team_member_property` needed to see this task — see Access & Role Visibility below |
| **Access Logic** | `ANY` (has at least one listed permission) or `ALL` (needs every listed permission) |
| **Aggregation** | How counts combine across multi-property selection — see Multi-Property Aggregation below |
| **Dismissible** | Whether the operator can snooze this card, and the reset rule |
| **Title Pattern** | Template for the card headline |
| **Subtitle** | Supporting context — tells the operator WHY to act |
| **CTA** | Button label (2 words max) |
| **Destination** | Where tapping the CTA navigates |
| **Icon** | Task-specific icon reference |
| **Urgency Text** | Optional due-state or deadline copy (e.g., "Due today", "Overdue 30+ days") |
| **Feature Gate** | Property-level flag that must be enabled, if any |
| **Status** | Live (new system) / Live (old system) / New / Deprecated |

---

## Source Classification

Every task has one source type that describes HOW the system knows to surface it:

| Source Type | What It Means | Example |
| --- | --- | --- |
| **System-detected** | Backend auto-detects from data — a threshold breach, missing record, or anomaly. No human triggered this. | Rent overdue >30 days (system scanned invoices and found defaults) |
| **Time-triggered** | Calendar or schedule-based. Fires on a day-of-week, date threshold, or recurring cadence. | Update Food Menu (every Monday for food-enabled properties) |
| **Event-driven** | A specific user action created this task. A tenant or team member did something that needs the manager's response. | Move-Out Request (tenant raised an eviction request) |

---

## Category Taxonomy

Categories are backend-driven — product/ops can add, rename, reorder, and move tasks between them without an app release. Current categories:

| Category | View All Chip | What It Covers |
| --- | --- | --- |
| Money | `Money` | Revenue protection, collections, settlements, refunds, payouts |
| People | `People` | Tenant lifecycle from booking through eviction and departure |
| Compliance | `Compliance` | KYC, agreements, police verification, legal requirements |
| Property | `Property` | Complaints, inspections, checklists, physical operations |
| Daily Ops | `Daily Ops` | Time-triggered routines: food, expenses, attendance, entry/exit |
| Growth | `Growth` | Leads, visits, booking funnel, app adoption, autopay setup |
| Platform | `Platform` | App updates, tutorials, plan expiry, platform health |

---

## Priority Color Legend

| Color | Hex | Meaning |
| --- | --- | --- |
| Red | `#FF3C30` | Urgent — needs immediate action. Blocks revenue, compliance, or tenant lifecycle. |
| Orange | `#EC9629` | Attention — should be done soon. Risks escalation if ignored. |
| Blue | `#1672EC` | Informational — review when convenient. Preparation or awareness. |
| Green | `#30B502` | Positive — upcoming event to prepare for. |

---

## Priority Framework

Every task has a **Priority Tier** (T1–T5) that determines its sort position within its category tab. Higher-tier tasks always appear above lower-tier tasks.

### Tier Definitions

| Tier | Name | Criteria | Typical Colors |
| --- | --- | --- | --- |
| **T1** | Revenue at Risk / SLA Breach | Money is being lost NOW, or someone is physically blocked from acting. Immediate financial or operational harm. | Red |
| **T2** | Needs Human Decision | Someone is waiting — a tenant, a team member, a vendor. Human approval, assignment, or review required. Delay causes frustration or escalation. | Red or Orange |
| **T3** | Upcoming Deadline | Not urgent today but becomes T1/T2 if ignored. Preparation window for approaching deadlines. | Orange or Blue |
| **T4** | Data Quality / Growth | Doesn't block operations but improves business health. Cumulative impact on revenue, compliance, or efficiency. | Blue |
| **T5** | Informational / Periodic | No urgency. Periodic nudge, awareness, or housekeeping. | Blue or Green |

### Sort Algorithm (Engineering Spec)

Within each category tab, tasks sort by:

1. **Tier** — T1 first, T5 last
2. **Sort signal value** — within same tier:
   - `amount`: highest ₹ amount first (more money at stake = more urgent)
   - `count`: highest count first (more items pending = more urgent)
   - `days_overdue`: most days past due first
   - `deadline`: closest deadline first (fewer days remaining = more urgent)
   - `static`: no dynamic signal; stable position per registry order
3. **Tiebreaker** — alphabetical by task_id for deterministic order

### Dynamic Tier Promotion

Some tasks escalate at runtime based on their data. The task's `Promotes To` field specifies the condition.

| Task | Base Tier | Promotes To | Condition |
| --- | --- | --- | --- |
| Online Settlement Pending (A12) | T3 | T2 | Any settlement pending > 5 business days |
| Staff Salary Due (A13) | T2 | T1 | Any salary > 7 days past due date |
| Stale Bookings (B8) | T3 | T2 | Any booking older than 14 days (double the base threshold) |
| Agreement Renewal Due (C1) | T3 | T2 | Any agreement overdue by > 30 days past expiry |
| KYC Pending (C5) | T3 | T2 | Police verification deadline within 7 days for any unverified tenant |
| KYC Credits Running Low (C8) | T3 | T2 | Credits = 0 (completely exhausted) |
| Complaints Unassigned (D1) | T2 | T1 | Any unassigned complaint > 24 hours old |
| Escalated Complaints L2 (D8) | T3 | T2 | Any L2 complaint approaching L3 threshold (72h) |
| Overbooked Rooms (D10) | T3 | T2 | An overbooked room has a new tenant move-in within 7 days |
| WhatsApp Balance Low (G2) | T3 | T1 | Balance = 0 (cannot send messages) |

---

## Access & Role Visibility

Every task specifies which permissions are required for it to be visible. Permissions reference columns on the `team_member_property` table.

### Resolution Rules

1. **Owner** → sees ALL tasks (always, no permission check)
2. **Admin designation** → sees ALL tasks (always — Admin auto-grants all permissions)
3. **Team member** → sees a task IF they have the required permission(s) on at least one of the filtered properties
4. **Universal tasks** (`Required Access: —`) → visible to everyone with app access
5. **Feature Gate** applies independently — even an Admin won't see a food task if the property doesn't have food enabled

### Access Logic

- `ANY` (default) — the team member has **at least one** of the listed permissions. E.g., `view_invoices, record_payment` with `ANY` means having either `view_invoices` OR `record_payment` is sufficient.
- `ALL` — the team member has **every** listed permission. Rare — used when the task requires compound capability.

### Permission-to-Category Quick Reference

| Category | Default Permission | Exceptions |
| --- | --- | --- |
| Money | `view_invoices` | Refunds → `add_refund_access`; Expenses → `view_expenses`; Settlement → `bank_access`; Salary → `view_team` |
| People | `view_tenants` | Eviction → `edit_eviction_access`; Checklist → `submit_or_update_moveout_checklist_access` |
| Compliance | `view_tenants` | All compliance tasks need tenant visibility |
| Property | `view_complaint` (complaints); `view_room` (rooms/inspections) | Meter → `edit_electricity_meter_status`; Checklist → `approve_moveout_checklist_access` |
| Daily Ops | Varies | Food → `view_food`; Expenses → `view_expenses`; Entry/exit → `view_tenants`; App update → `—` (universal) |
| Growth | `view_leads` (lead tasks); `view_tenants` (adoption tasks) | — |
| Platform | `—` (universal) | WhatsApp balance → `view_tenants` |

---

## Assignee Visibility (Admin View)

When an Admin or Owner views a task card, they also see **who is responsible** for that task — which team members have the required permissions and can act on it.

### How Assignees Are Determined

"Assignee" is NOT a person manually assigned to the task. It is the set of team members who have the `Required Access` permissions for that task on the filtered properties. This is computed at read time from `team_member_property`, not stored.

### Display Rules

| Viewer | What They See |
| --- | --- |
| Owner / Admin | Assignee chip below the CTA: `"Sanchay + 2 assigned"` or `"Unassigned"` |
| Non-admin team member | No assignee info — just the task card |

### Formatting

| Assignee Count | Display |
| --- | --- |
| 0 | `"Unassigned"` — red chip. Nobody has the required permission. |
| 1 | `"Sanchay"` — just the name |
| 2+ | `"Sanchay + {N-1} assigned"` — first alphabetically, or `"You + {N-1}"` if the admin viewer is in the set |

### Multi-Property Assignees

When viewing multiple properties, assignees are deduplicated by person. If Sanchay has `view_invoices` on Property A and Property B, they count as 1 assignee, not 2.

### Exceptions

Some tasks have a different assignee source than their `Required Access`. For example, complaints may use the `ComplaintResponderMap` (who is the assigned responder) rather than everyone with `view_complaint`. These are noted per task as `Assignee Source` overrides.

---

## Multi-Property Aggregation

When the manager selects multiple properties as scope (via `pg_number_filter=1,2,3`), tasks aggregate across all selected properties into a single card per task type.

### Aggregation Rules

- **Count**: Sum of counts across all properties where the task is active. E.g., 2 move-out requests from Property A + 3 from Property C = `5 Move-Out Requests`
- **Amount**: Sum of amounts. E.g., ₹45K overdue from Prop A + ₹80K from Prop B = `₹1.25L`
- **Property attribution**: The card shows `"from {N} properties"` as a secondary label when N > 1. When N = 1, omit the label.
- **Zero-count properties excluded**: If Property B has 0 complaints, only A and C count — N = 2, not 3.
- **Feature-gated aggregation**: Only aggregate from properties where the feature is enabled. Food menu task counts only from food-enabled properties.
- **Role-filtered aggregation**: For team members, only aggregate from properties where they have the required permission. A team member with `view_complaints` on Prop A but not Prop B sees only Prop A's complaint count.

### Aggregation Types

| Type | How It Works | Used For |
| --- | --- | --- |
| `sum` | Add counts from all properties (default) | Most tasks — move-in requests, complaints, overdue rent |
| `max` | Show the worst case across properties | Urgency text values like "days overdue" |
| `any` | Show if ANY property matches, count = 1 | Binary tasks like "App Update Available", "Plan Expiring" |

### Card Display (Multi-Property)

```
┌─────────────────────────────────────────┐
│  5 Move-Out Requests                    │
│  Approve or extend — clear pending queue│
│  from 3 properties            [Review →]│
│  Sanchay + 2 assigned                   │
└─────────────────────────────────────────┘
```

The `"from {N} properties"` line appears between subtitle and CTA when N > 1. On tap, the app can expand to show per-property breakdown (future enhancement — not in v1).

---

---

## A. MONEY

Tasks under the `Money` category chip in View All. Revenue protection, collections, settlements, refunds.

---

### A1. Rent & Bills Overdue

| Field | Detail |
| --- | --- |
| **Task Name** | Rent & Bills Overdue — ₹{amount} |
| **Source Type** | System-detected |
| **Section** | Money |
| **Meaning** | Count of tenants with rent invoices unpaid for more than 30 days. Title includes the total unpaid amount across all invoice types. |
| **Operator Description** | "How many tenants have rent overdue by more than a month, and how much do they owe in total?" These are your chronic non-payers. The number shows tenant count; the amount shows everything unpaid (not just rent). |
| **Calculation Logic** | **Count:** `COUNT(DISTINCT tenant) WHERE status = 1 AND invoice.status = 0 (unpaid) AND due_type = 'Rent' AND (today - due_date) > 30 days`. **Amount in title:** `SUM(all unpaid invoices)` for the same tenants (all due types, not just rent). |
| **Source** | `getAllTenants.ts:2670` (old system). Not yet in new homepage system. |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Red |
| **Priority Tier** | T1 |
| **Sort Signal** | `amount` |
| **Promotes To** | — (already T1) |
| **Required Access** | `view_invoices` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | No |
| **Title Pattern** | `{N} Rent Dues Overdue — ₹{amount}` |
| **Subtitle** | Follow up now — these tenants are 30+ days past due |
| **CTA** | `Collect Dues` |
| **Destination** | Tenant list filtered to rent defaulters (filter_code: 5001) |
| **Icon** | `bills.png` |
| **Urgency Text** | `Overdue 30+ days` |
| **Feature Gate** | None |
| **Status** | Live (old system) — needs migration to new homepage |

---

### A2. Payments Not Linked

| Field | Detail |
| --- | --- |
| **Task Name** | Transactions Not Linked |
| **Source Type** | System-detected |
| **Section** | Money |
| **Meaning** | Bank transactions received but not yet matched to a tenant or bill |
| **Operator Description** | "How many payments came in that you haven't matched to a tenant yet?" Unlinked payments mean your books don't reconcile. Match them to keep your ledger clean. |
| **Calculation Logic** | `COUNT(*) FROM invoices WHERE status = 1 AND is_active = 1 AND payer LIKE 'cust_%'` |
| **Source** | `getPendingTasks() in v1/homepage/service.ts` — query ~L1371, card push ~L1584 (new system) |
| **Visibility** | Always shown when count > 0. Dismissible with midnight reset. |
| **Priority Color** | Orange |
| **Priority Tier** | T2 |
| **Sort Signal** | `count` |
| **Promotes To** | — |
| **Required Access** | `view_invoices, record_payment` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | Yes — midnight reset |
| **Title Pattern** | `{N} Transaction(s) Not Linked` |
| **Subtitle** | Match them to avoid reconciliation gaps |
| **CTA** | `Link Now` |
| **Destination** | List of payments not linked with tenants |
| **Icon** | `payment_link.png` |
| **Urgency Text** | None |
| **Feature Gate** | None |
| **Status** | Live (new system) |

---

### A3. Token Payments to Collect

| Field | Detail |
| --- | --- |
| **Task Name** | Token Payments to Collect |
| **Source Type** | System-detected |
| **Section** | Money |
| **Meaning** | Bookings that don't have any paid invoice on record |
| **Operator Description** | "How many bookings haven't paid their token or advance amount yet?" No payment received at all — collect before their move-in date or the booking is hollow. |
| **Calculation Logic** | `COUNT(DISTINCT tenant) WHERE status = 2 AND NOT EXISTS(invoice WHERE payer = tenant.firebase_id AND status = 1)` |
| **Source** | `getAllTenants.ts:2954` (old system) |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Red |
| **Priority Tier** | T1 |
| **Sort Signal** | `count` |
| **Promotes To** | — |
| **Required Access** | `view_invoices, view_tenants` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | No |
| **Title Pattern** | `{N} Token Payment(s) to Collect` |
| **Subtitle** | No advance received — collect before move-in |
| **CTA** | `Collect Now` |
| **Destination** | Booking list filtered to unpaid tokens (filter_code: 7003) |
| **Icon** | `money.png` |
| **Urgency Text** | None |
| **Feature Gate** | None |
| **Status** | Live (old system) — needs migration to new homepage |

---

### A4. Final Dues to Collect

| Field | Detail |
| --- | --- |
| **Task Name** | Final Dues to Collect — ₹{amount} |
| **Source Type** | System-detected |
| **Section** | Money |
| **Meaning** | Old/evicted tenants who still have unpaid invoices |
| **Operator Description** | "How many ex-tenants still owe you money, and how much?" These people left but didn't clear all their bills. The longer you wait, the harder it is to recover. |
| **Calculation Logic** | **Count:** `COUNT(DISTINCT tenant) WHERE status = 0 AND invoice.status = 4`. **Amount:** `SUM(invoice.amount) WHERE status = 4`. |
| **Source** | `getAllTenants.ts:2850` (old system) |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Red |
| **Priority Tier** | T1 |
| **Sort Signal** | `amount` |
| **Promotes To** | — |
| **Required Access** | `view_invoices` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | No |
| **Title Pattern** | `{N} Final Due(s) to Collect — ₹{amount}` |
| **Subtitle** | Ex-tenants with outstanding balances — recover before losing contact |
| **CTA** | `Collect Dues` |
| **Destination** | Old Tenants list filtered to outstanding dues (filter_code: 3001) |
| **Icon** | `money_copy.png` |
| **Urgency Text** | None |
| **Feature Gate** | None |
| **Status** | Live (old system) — needs migration to new homepage |

---

### A5. Deposits to Refund

| Field | Detail |
| --- | --- |
| **Task Name** | Deposits to Refund — ₹{amount} |
| **Source Type** | System-detected |
| **Section** | Money |
| **Meaning** | Old tenants who have a positive refundable deposit balance |
| **Operator Description** | "How many ex-tenants are you still holding deposit money for?" You need to return their security deposit minus any deductions. Delays cause disputes and legal risk. |
| **Calculation Logic** | **Count:** Number of old tenants where `refundable_amount > 0` (computed via `getRefundableAmount()` — deposits paid minus refunds issued minus deposit adjustments). **Amount:** Sum of all positive refundable amounts. |
| **Source** | `getAllTenants.ts:2857`, helper: `getAllTenants.ts:2984` (old system) |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Orange |
| **Priority Tier** | T2 |
| **Sort Signal** | `amount` |
| **Promotes To** | — |
| **Required Access** | `add_refund_access` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | Yes — midnight reset |
| **Title Pattern** | `{N} Deposit(s) to Refund — ₹{amount}` |
| **Subtitle** | Process refunds before tenants escalate |
| **CTA** | `Review Refunds` |
| **Destination** | Old Tenants list > Deposit Refund Pending (filter_code: 3002) |
| **Icon** | `money-arrow.png` |
| **Urgency Text** | None |
| **Feature Gate** | None |
| **Status** | Live (old system) — needs migration to new homepage |

---

### A6. Settlement Failed

| Field | Detail |
| --- | --- |
| **Task Name** | Settlement Failed |
| **Source Type** | System-detected |
| **Section** | Money |
| **Meaning** | Bank payout attempts that failed — money collected from tenants but not transferred to the operator's bank |
| **Operator Description** | "Did any of your bank payouts fail?" Collected rent is stuck in the payment gateway. Usually a bank detail issue — fix it or the money stays in limbo. |
| **Calculation Logic** | `COUNT(*) FROM settlement_scheduler WHERE status = 3 (failure) AND is_active = 1` |
| **Source** | New — not yet implemented. Entity: `settlement_scheduler` with status field. |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Red |
| **Priority Tier** | T1 |
| **Sort Signal** | `amount` |
| **Promotes To** | — |
| **Required Access** | `bank_access` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | No |
| **Title Pattern** | `{N} Settlement(s) Failed — ₹{amount}` |
| **Subtitle** | Payouts to your bank failed — update bank details |
| **CTA** | `Fix Now` |
| **Destination** | Settlement list filtered to failed |
| **Icon** | `settlement_failed.png` |
| **Urgency Text** | `Action needed` |
| **Feature Gate** | None |
| **Status** | New |

---

### A7. AutoPay Debits Failed

| Field | Detail |
| --- | --- |
| **Task Name** | AutoPay Debits Failed |
| **Source Type** | System-detected |
| **Section** | Money |
| **Meaning** | Automatic rent collection attempts that failed after retries |
| **Operator Description** | "Which tenants' auto-pay failed this cycle?" The system tried to auto-collect but the debit bounced. Follow up manually or the rent goes uncollected. |
| **Calculation Logic** | `COUNT(*) FROM autopay_debit_schedule WHERE status = 3 (SKIPPED — retries exhausted) OR (status = 0 (PENDING) AND retry_count >= 3)`. Status enum: 0=PENDING, 1=PROCESSING, 2=RESOLVED, 3=SKIPPED, 4=INTERIM. Retry cap is hardcoded to 3, not a column. |
| **Source** | New — not yet implemented. Entity: `autopay_debit_schedule.ts` — numeric status enum `AutopayDebitScheduleStatus`, `retry_count` field (line 49). |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Red |
| **Priority Tier** | T1 |
| **Sort Signal** | `count` |
| **Promotes To** | — |
| **Required Access** | `view_invoices` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | No |
| **Title Pattern** | `{N} AutoPay Debit(s) Failed` |
| **Subtitle** | Auto-collect bounced — follow up with these tenants |
| **CTA** | `Follow Up` |
| **Destination** | AutoPay list filtered to failed debits |
| **Icon** | `autopay_failed.png` |
| **Urgency Text** | None |
| **Feature Gate** | Property must have autopay enabled |
| **Status** | New |

---

### A8. Wallet Payout Failed

| Field | Detail |
| --- | --- |
| **Task Name** | Payout Failed |
| **Source Type** | System-detected |
| **Section** | Money |
| **Meaning** | FlexiPe/wallet payout to operator's bank that failed |
| **Operator Description** | "Did your wallet payout to your bank fail?" Usually a bank detail mismatch. Your collected money is stuck until you fix it. |
| **Calculation Logic** | `COUNT(*) FROM wallet_payouts WHERE status = -1 AND is_active = 1` |
| **Source** | New — not yet implemented. Entity: `wallet_payouts` with status = -1 for failure. |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Red |
| **Priority Tier** | T1 |
| **Sort Signal** | `count` |
| **Promotes To** | — |
| **Required Access** | `bank_access` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | No |
| **Title Pattern** | `{N} Payout(s) Failed` |
| **Subtitle** | Bank transfer failed — verify your account details |
| **CTA** | `Fix Now` |
| **Destination** | Wallet payouts screen |
| **Icon** | `payout_failed.png` |
| **Urgency Text** | `Action needed` |
| **Feature Gate** | FlexiPe/Wallet enabled |
| **Status** | New |

---

### A9. RentOk Charges Due

| Field | Detail |
| --- | --- |
| **Task Name** | Platform Charges Due |
| **Source Type** | Time-triggered |
| **Section** | Money |
| **Meaning** | RentOk subscription or platform charges that are due |
| **Operator Description** | "Is your RentOk subscription payment pending?" If unpaid, features may get restricted. |
| **Calculation Logic** | `COUNT(*) FROM rentok_charges_scheduler WHERE status IN (0, 1) AND is_active = 1 AND start_date <= CURRENT_DATE` |
| **Source** | New — not yet implemented. Entity: `rentok_charges_scheduler`. |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Red |
| **Priority Tier** | T1 |
| **Sort Signal** | `amount` |
| **Promotes To** | — |
| **Required Access** | `—` (universal — owner/admin concern, but visible to all for awareness) |
| **Access Logic** | ANY |
| **Aggregation** | `any` |
| **Dismissible** | No |
| **Title Pattern** | `Platform Charges Due` |
| **Subtitle** | RentOk subscription pending — avoid service disruption |
| **CTA** | `Pay Now` |
| **Destination** | Billing/subscription screen |
| **Icon** | `platform_charges.png` |
| **Urgency Text** | `Due now` |
| **Feature Gate** | None |
| **Status** | New |

---

### A10. AutoPay Manual Review

| Field | Detail |
| --- | --- |
| **Task Name** | AutoPay Payments to Review |
| **Source Type** | System-detected |
| **Section** | Money |
| **Meaning** | AutoPay transactions that arrived but couldn't be auto-applied — needs manual matching |
| **Operator Description** | "Which autopay payments need manual review?" The money came in, but the system couldn't figure out which tenant or invoice it belongs to. Match it manually. |
| **Calculation Logic** | `COUNT(*) FROM autopay_transaction WHERE status = 'MANUAL_REVIEW'`. Note: entity has no `is_active` column — filter on status alone. |
| **Source** | New — entity: `autopay_transaction.ts` with `AutopayTransactionStatus.MANUAL_REVIEW`. |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Orange |
| **Priority Tier** | T2 |
| **Sort Signal** | `count` |
| **Promotes To** | — |
| **Required Access** | `view_invoices, record_payment` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | Yes — midnight reset |
| **Title Pattern** | `{N} AutoPay Payment(s) to Review` |
| **Subtitle** | Money received but not matched — link to the right tenant |
| **CTA** | `Review Now` |
| **Destination** | AutoPay transactions filtered to manual review |
| **Icon** | `autopay_review.png` |
| **Urgency Text** | None |
| **Feature Gate** | Property must have autopay enabled |
| **Status** | New |

---

### A11. Invoice Generation Failed

| Field | Detail |
| --- | --- |
| **Task Name** | Invoice Generation Failed |
| **Source Type** | System-detected |
| **Section** | Money |
| **Meaning** | Scheduled rent invoices that failed to generate |
| **Operator Description** | "Did any rent invoices fail to generate this cycle?" The scheduler tried to create invoices but some failed. These tenants won't get billed until you investigate. |
| **Calculation Logic** | `COUNT(*) FROM tenant_rent_generation WHERE is_generated = 0 AND due_date <= CURRENT_DATE`. Note: table name is singular `tenant_rent_generation`, not plural. |
| **Source** | New — entity: `tenant_rent_generations.ts` (file name has plural, table name is singular). |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Red |
| **Priority Tier** | T1 |
| **Sort Signal** | `count` |
| **Promotes To** | — |
| **Required Access** | `view_invoices, add_invoices` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | No |
| **Title Pattern** | `{N} Invoice(s) Failed to Generate` |
| **Subtitle** | Tenants won't be billed until fixed — investigate now |
| **CTA** | `Fix Now` |
| **Destination** | Invoice generation log / failing tenants list |
| **Icon** | `invoice_failed.png` |
| **Urgency Text** | `Revenue blocked` |
| **Feature Gate** | None |
| **Status** | New |

---

### A12. Online Settlement Pending

| Field | Detail |
| --- | --- |
| **Task Name** | Online Settlements Pending |
| **Source Type** | System-detected |
| **Section** | Money |
| **Meaning** | Online payments collected but settlement to bank is still in processing |
| **Operator Description** | "How many online settlements are still processing?" Money collected from tenants but not yet in your bank. Usually resolves in 2-3 days — flag if older than that. |
| **Calculation Logic** | `COUNT(*) FROM settlement_scheduler WHERE status IN (0, 2) AND is_active = 1` — status 0=initiated, 2=attempted. Note: status 1=success (NOT in-progress), 3=failure. |
| **Source** | New — entity: `settlement_scheduler` with status field. |
| **Visibility** | Always shown when count > 0 and oldest pending > 3 days |
| **Priority Color** | Orange |
| **Priority Tier** | T3 |
| **Sort Signal** | `days_overdue` |
| **Promotes To** | T2 when any settlement pending > 5 days |
| **Required Access** | `bank_access` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | Yes — midnight reset |
| **Title Pattern** | `{N} Settlement(s) Pending` |
| **Subtitle** | Online payments awaiting bank transfer |
| **CTA** | `View Status` |
| **Destination** | Settlement list filtered to pending |
| **Icon** | `settlement_pending.png` |
| **Urgency Text** | `{N} days in processing` |
| **Feature Gate** | None |
| **Status** | New |

---

### A13. Staff Salary & Reimbursement Due

| Field | Detail |
| --- | --- |
| **Task Name** | Staff Payments Due |
| **Source Type** | Time-triggered |
| **Section** | Money |
| **Meaning** | Team salaries, bonuses, or reimbursements that are due but unpaid |
| **Operator Description** | "Are any staff payments overdue?" Delayed salary or reimbursement hurts team morale and retention. Pay on time. |
| **Calculation Logic** | `COUNT(*) FROM team_salaries WHERE is_paid = 0 AND due_date <= CURRENT_DATE` — includes type: salary, bonus, reimbursement |
| **Source** | New — entity: `teamSalaries.ts` with `is_paid` (default 0), `due_date`, `type` fields. |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Orange |
| **Priority Tier** | T2 |
| **Sort Signal** | `days_overdue` |
| **Promotes To** | T1 when any salary > 7 days overdue |
| **Required Access** | `view_team` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | Yes — midnight reset |
| **Title Pattern** | `{N} Staff Payment(s) Due` |
| **Subtitle** | Salaries or reimbursements overdue — pay your team on time |
| **CTA** | `Pay Now` |
| **Destination** | Team salaries screen |
| **Icon** | `team_salary.png` |
| **Urgency Text** | `Overdue` |
| **Feature Gate** | None |
| **Cross-Reference** | Same underlying data as E11 (Team Salary Due) in Daily Ops. A13 includes salary + bonus + reimbursement under Money; E11 is the salary-focused view under Daily Ops. Implementation must deduplicate: either show in one category only, or differentiate scope (A13 = all payment types, E11 = salary-only). |
| **Status** | New |

---

---

## B. PEOPLE

Tasks under the `People` category chip in View All. Covers the full tenant lifecycle — from booking requests to active tenancy to eviction and departure.

---

### B1. Booking Requests to Approve

| Field | Detail |
| --- | --- |
| **Task Name** | Move-In Requests |
| **Source Type** | Event-driven |
| **Section** | People |
| **Meaning** | New joining/booking requests not yet reviewed by the manager |
| **Operator Description** | "How many people have booked a bed but you haven't confirmed yet?" Every unapproved booking is a bed that's blocked but not confirmed — approve or reject so the bed is either occupied or freed. |
| **Calculation Logic** | `COUNT(DISTINCT tenant) WHERE status = 2 AND NOT EXISTS(tenant_booking_confirmation WHERE is_confirmed = 1)` |
| **Source** | `service.ts:1529` (new system — **now commented-out dead code**; the `move_in_request_pending` push and its `moveInReqRows` query at `service.ts:1382` are disabled), `getAllTenants.ts:3826` (old system, filter_code: 1700 — `numCode == 1700` confirmed-bookings branch; approvals-pending uses its inverse) |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Red |
| **Priority Tier** | T1 |
| **Sort Signal** | `count` |
| **Promotes To** | — |
| **Required Access** | `view_tenants, add_tenants` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | No |
| **Title Pattern** | `{N} Move-In Request(s)` |
| **Subtitle** | Review to approve or reject joining |
| **CTA** | `Approve Joining` |
| **Destination** | Joining requests worklist |
| **Icon** | `user.png` |
| **Urgency Text** | None |
| **Feature Gate** | None |
| **Status** | Live (old system only) — the new-system push (`move_in_request_pending`, filter_code 1700) is commented-out dead code; see Source. Note: a separate live new-system task `joining_request_pending` (filter_code 304) exists but is not catalogued here. |

---

### B2. Move-Out Requests

| Field | Detail |
| --- | --- |
| **Task Name** | Move-Out Requests |
| **Source Type** | Event-driven |
| **Section** | People |
| **Meaning** | Tenants who have active eviction requests pending manager approval |
| **Operator Description** | "How many tenants have move-out requests waiting for your approval?" Someone (a tenant or team member) has initiated an eviction — you need to review it before the move-out date. |
| **Calculation Logic** | New system: `COUNT(*) FROM tenant_eviction_details WHERE status = 0 AND is_active = 1 AND current month`. Old system: `status = 2 AND is_active = 1`. |
| **Source** | `service.ts:1542` (new system — `move_out_request_pending`, count query at `service.ts:1417`), `getAllTenants.ts:3839` (old system, filter_code: 120090) |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Red |
| **Priority Tier** | T1 |
| **Sort Signal** | `deadline` |
| **Promotes To** | — |
| **Required Access** | `edit_eviction_access` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | No |
| **Title Pattern** | `{N} Move-Out Request(s)` |
| **Subtitle** | Collect/adjust dues before they leave |
| **CTA** | `Settle Dues` |
| **Destination** | Move-out/eviction worklist |
| **Icon** | `eviction_request.png` |
| **Urgency Text** | None |
| **Feature Gate** | None |
| **Cross-Reference** | B2 and B10 share the same underlying entity (`tenant_eviction_details`) but are mutually exclusive: B2 applies to properties NOT on the new eviction flow (status=0 in new system, status=2 in old system); B10 applies only when `is_new_eviction_flow = true` (status=2 in new flow means "requested"). A property will never trigger both. |
| **Status** | Live (both systems). Note: eviction status differs between systems (0 vs 2) — needs reconciliation. |

---

### B3. Eviction Extensions to Review

| Field | Detail |
| --- | --- |
| **Task Name** | Extension Requests |
| **Source Type** | Event-driven |
| **Section** | People |
| **Meaning** | Tenants under eviction who have requested a stay extension that hasn't been approved yet |
| **Operator Description** | "How many tenants being asked to leave have requested more time?" These tenants were given a move-out date but want to extend. Approve or deny — leaving them hanging blocks bed planning. |
| **Calculation Logic** | `COUNT(DISTINCT tenant) WHERE status = 1 AND tenant_eviction_details.is_active = 1 AND tenant_extension_requests.is_approved = 0` |
| **Source** | `getAllTenants.ts:3905` (old system, filter_code: 120093) |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Orange |
| **Priority Tier** | T2 |
| **Sort Signal** | `deadline` |
| **Promotes To** | — |
| **Required Access** | `edit_eviction_access` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | No |
| **Title Pattern** | `{N} Extension Request(s) Pending` |
| **Subtitle** | Tenants under notice want more time — approve or deny |
| **CTA** | `Review Now` |
| **Destination** | Extension requests list |
| **Icon** | `eviction_extension.png` |
| **Urgency Text** | None |
| **Feature Gate** | None |
| **Status** | Live (old system) — needs migration to new homepage |

---

### B4. Rate Departing Tenants

| Field | Detail |
| --- | --- |
| **Task Name** | Departing Tenants to Rate |
| **Source Type** | System-detected |
| **Section** | People |
| **Meaning** | Tenants under active eviction who haven't been rated by the manager |
| **Operator Description** | "How many leaving tenants have you not rated yet?" Rate them before they leave so future operators or properties can see tenant quality. Once they're gone, you'll forget. |
| **Calculation Logic** | `COUNT(DISTINCT tenant) WHERE status = 1 AND tenant_eviction_details.is_active = 1 AND tenant_eviction_details.rating IS NULL` |
| **Source** | `getAllTenants.ts:4190` (old system, filter_code: 5008) |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Blue |
| **Priority Tier** | T4 |
| **Sort Signal** | `count` |
| **Promotes To** | — |
| **Required Access** | `view_tenants` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | Yes — midnight reset |
| **Title Pattern** | `{N} Departing Tenant(s) to Rate` |
| **Subtitle** | Rate before they leave — you won't remember later |
| **CTA** | `Rate Now` |
| **Destination** | Tenant list filtered to unrated evictions |
| **Icon** | `rating.png` |
| **Urgency Text** | None |
| **Feature Gate** | None |
| **Status** | Live (old system) — needs migration to new homepage |

---

### B5. Keys to Collect

| Field | Detail |
| --- | --- |
| **Task Name** | Keys to Collect |
| **Source Type** | System-detected |
| **Section** | People |
| **Meaning** | Tenants under active eviction at properties where key handover is enabled |
| **Operator Description** | "How many tenants leaving still need to return their room keys?" Only relevant if you've turned on the handover feature. Don't let anyone leave without completing handover. |
| **Calculation Logic** | `COUNT(DISTINCT tenant) WHERE status = 1 AND tenant_eviction_details.is_active = 1 AND property.complete_handover_enabled = 1` |
| **Source** | `getAllTenants.ts:3852` (old system, filter_code: 120091) |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Orange |
| **Priority Tier** | T2 |
| **Sort Signal** | `count` |
| **Promotes To** | — |
| **Required Access** | `key_handover_access` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | Yes — midnight reset |
| **Title Pattern** | `{N} Key(s) to Collect` |
| **Subtitle** | Complete handover before tenants leave the property |
| **CTA** | `View Handover` |
| **Destination** | Handover/key collection worklist |
| **Icon** | `checklist.png` |
| **Urgency Text** | None |
| **Feature Gate** | `complete_handover_enabled = 1` |
| **Status** | Live (old system) — needs migration to new homepage |

---

### B6. Move-Ins This Week

| Field | Detail |
| --- | --- |
| **Task Name** | Move-Ins This Week |
| **Source Type** | Time-triggered |
| **Section** | People |
| **Meaning** | Bookings with a joining date within the next 7 days |
| **Operator Description** | "How many new tenants are arriving this week?" Prepare rooms, keys, and onboarding docs. This is a preparation task, not a problem — but missing it means a bad first impression. |
| **Calculation Logic** | `COUNT(DISTINCT tenant) WHERE status = 2 AND date_of_joining > today AND date_of_joining <= (today + 7 days)` |
| **Source** | `getAllTenants.ts:3786` (old system, filter_code: 1504) |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Green |
| **Priority Tier** | T3 |
| **Sort Signal** | `deadline` |
| **Promotes To** | — |
| **Required Access** | `view_tenants` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | Yes — midnight reset |
| **Title Pattern** | `{N} Move-In(s) This Week` |
| **Subtitle** | Prepare rooms and onboarding docs |
| **CTA** | `View Bookings` |
| **Destination** | Booking list filtered to this week's move-ins |
| **Icon** | `key.png` |
| **Urgency Text** | `This week` |
| **Feature Gate** | None |
| **Status** | Live (old system) — needs migration to new homepage |

---

### B7. Bookings Without Room

| Field | Detail |
| --- | --- |
| **Task Name** | Bookings Without Room |
| **Source Type** | System-detected |
| **Section** | People |
| **Meaning** | Bookings that haven't been assigned to a specific room yet |
| **Operator Description** | "How many booked tenants don't have a room assigned?" They've booked but you haven't told them where they'll stay. Assign rooms before move-in day or it's chaos. |
| **Calculation Logic** | `COUNT(DISTINCT tenant) WHERE status = 2 AND room IS NULL` |
| **Source** | `getAllTenants.ts:3946` (old system, filter_code: 4003) |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Orange |
| **Priority Tier** | T2 |
| **Sort Signal** | `count` |
| **Promotes To** | — |
| **Required Access** | `view_tenants, view_room` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | Yes — midnight reset |
| **Title Pattern** | `{N} Booking(s) Without Room` |
| **Subtitle** | Assign rooms before move-in day |
| **CTA** | `Assign Rooms` |
| **Destination** | Booking list filtered to unassigned rooms |
| **Icon** | `user.png` |
| **Urgency Text** | None |
| **Feature Gate** | None |
| **Status** | Live (old system) — needs migration to new homepage |

---

### B8. Stale Bookings

| Field | Detail |
| --- | --- |
| **Task Name** | Stale Bookings |
| **Source Type** | System-detected |
| **Section** | People |
| **Meaning** | Bookings older than 7 days that haven't been confirmed or converted to active tenants |
| **Operator Description** | "How many bookings have been sitting for more than a week?" Stale bookings block beds without generating revenue. Convert them or cancel to free the inventory. |
| **Calculation Logic** | `COUNT(DISTINCT tenant) WHERE status = 2 AND created_at < (CURRENT_DATE - INTERVAL '7 days') AND NOT EXISTS(tenant_booking_confirmation WHERE is_confirmed = 1)` |
| **Source** | New — not yet implemented. Uses existing `tenant` table with date filter. |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Orange |
| **Priority Tier** | T3 |
| **Sort Signal** | `days_overdue` |
| **Promotes To** | T2 when any booking > 14 days stale |
| **Required Access** | `view_tenants` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | Yes — midnight reset |
| **Title Pattern** | `{N} Stale Booking(s)` |
| **Subtitle** | Bookings older than 7 days — convert or cancel to free beds |
| **CTA** | `Review Now` |
| **Destination** | Booking list filtered to stale (>7 days) |
| **Icon** | `stale_booking.png` |
| **Urgency Text** | `Older than 7 days` |
| **Feature Gate** | None |
| **Status** | New |

---

### B9. Tenants to Install the App

| Field | Detail |
| --- | --- |
| **Task Name** | Tenants to Install App |
| **Source Type** | System-detected |
| **Section** | People |
| **Meaning** | Active tenants who haven't downloaded the RentOk tenant app |
| **Operator Description** | "How many tenants still don't have the app?" Without the app they can't pay online, raise complaints, or see invoices. Every tenant off the app is more manual work for you. |
| **Calculation Logic** | `COUNT(DISTINCT tenant) WHERE status = 1 AND onboarding_flag = false` |
| **Source** | `getAllTenants.ts:3037` (old system, filter_code: 102) |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Blue |
| **Priority Tier** | T4 |
| **Sort Signal** | `count` |
| **Promotes To** | — |
| **Required Access** | `view_tenants` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | Yes — midnight reset |
| **Title Pattern** | `{N} Tenant(s) Without App` |
| **Subtitle** | Nudge them to install — saves you manual work |
| **CTA** | `Send Invite` |
| **Destination** | Tenant list filtered to no app installed |
| **Icon** | `app_download.png` |
| **Urgency Text** | None |
| **Feature Gate** | None |
| **Cross-Reference** | Same data as F5 (Tenants Not On App) in Growth. B9 = People/onboarding framing; F5 = Growth/efficiency framing. Show in the active category's tab only, not both simultaneously. |
| **Status** | Live (old system) — needs migration to new homepage |

---

### B10. Eviction Requests Pending Approval

| Field | Detail |
| --- | --- |
| **Task Name** | Eviction Requests |
| **Source Type** | Event-driven |
| **Section** | People |
| **Meaning** | Tenants whose eviction has been requested but not yet approved by the manager (new eviction flow) |
| **Operator Description** | "How many tenants have eviction requests waiting for your decision?" Under the new eviction flow, requests are raised and need manager approval before the process starts. |
| **Calculation Logic** | `COUNT(*) FROM tenant_eviction_details WHERE status = 2 AND is_active = 1` — status 2 = requested (new flow) |
| **Source** | `getAllTenants.ts:3839` (old system, filter_code: 120090; `is_new_eviction_flow` branch at `getAllTenants.ts:2021`). |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Red |
| **Priority Tier** | T2 |
| **Sort Signal** | `count` |
| **Promotes To** | — |
| **Required Access** | `edit_eviction_access` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | No |
| **Title Pattern** | `{N} Eviction Request(s) Pending` |
| **Subtitle** | Approve or reject before move-out process stalls |
| **CTA** | `Review Now` |
| **Destination** | Eviction requests worklist |
| **Icon** | `eviction_request.png` |
| **Urgency Text** | None |
| **Feature Gate** | `is_new_eviction_flow = true` |
| **Status** | New |

---

### B11. Guest Visit Requests Pending

| Field | Detail |
| --- | --- |
| **Task Name** | Guest Visit Requests |
| **Source Type** | Event-driven |
| **Section** | People |
| **Meaning** | Tenants who have registered guest visits waiting for manager approval |
| **Operator Description** | "How many guest visit requests are waiting for approval?" Tenants registered guests through the app — approve or decline before the guest arrives. |
| **Calculation Logic** | `COUNT(*) FROM host_friend WHERE status = 'pending' AND property_id = ANY(...)` |
| **Source** | New — entity: `host_friend.ts` with status enum ['pending', 'approved', 'declined']. |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Orange |
| **Priority Tier** | T2 |
| **Sort Signal** | `deadline` |
| **Promotes To** | — |
| **Required Access** | `view_tenants` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | Yes — midnight reset |
| **Title Pattern** | `{N} Guest Visit Request(s)` |
| **Subtitle** | Approve before the guest arrives at the gate |
| **CTA** | `Review Now` |
| **Destination** | Guest visit requests list |
| **Icon** | `guest_visit.png` |
| **Urgency Text** | None |
| **Feature Gate** | `tenant_app_host_friends` property setting enabled |
| **Status** | New |

---

### B12. Move-Outs This Month

| Field | Detail |
| --- | --- |
| **Task Name** | Move-Outs This Month |
| **Source Type** | Time-triggered |
| **Section** | People |
| **Meaning** | Tenants with move-out dates falling within the current month |
| **Operator Description** | "How many tenants are leaving this month?" Prepare: collect outstanding dues, complete move-out checklists, process deposit refunds, and get the room ready for the next tenant. |
| **Calculation Logic** | `COUNT(DISTINCT tenant) WHERE status = 1 AND date_of_eviction >= FIRST_OF_MONTH AND date_of_eviction <= LAST_OF_MONTH AND tenant_eviction_details.is_active = 1` |
| **Source** | `getAllTenants.ts:3424` (old system, filter_code: 610) |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Green |
| **Priority Tier** | T3 |
| **Sort Signal** | `deadline` |
| **Promotes To** | — |
| **Required Access** | `view_tenants` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | Yes — midnight reset |
| **Title Pattern** | `{N} Move-Out(s) This Month` |
| **Subtitle** | Prepare rooms, collect dues, process refunds |
| **CTA** | `View List` |
| **Destination** | Tenant list filtered to move-outs this month (filter_code: 610) |
| **Icon** | `move_out.png` |
| **Urgency Text** | `This month` |
| **Feature Gate** | None |
| **Status** | New |

---

### B13. Tenants Without Contact Info

| Field | Detail |
| --- | --- |
| **Task Name** | Contact Info Missing |
| **Source Type** | System-detected |
| **Section** | People |
| **Meaning** | Active tenants without a valid phone number on file |
| **Operator Description** | "How many tenants don't have a phone number?" Without a valid number, the system can't send rent reminders, WhatsApp alerts, or payment links. This blocks all automated communication. |
| **Calculation Logic** | `COUNT(DISTINCT tenant) WHERE status = 1 AND (phone IS NULL OR phone = '' OR LENGTH(phone) != 10)` |
| **Source** | `getAllTenants.ts:3182` (old system, filter_code: 305) |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Orange |
| **Priority Tier** | T4 |
| **Sort Signal** | `count` |
| **Promotes To** | — |
| **Required Access** | `view_tenants, edit_tenants` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | Yes — midnight reset |
| **Title Pattern** | `{N} Tenant(s) Without Contact` |
| **Subtitle** | No phone number — blocks reminders and payment links |
| **CTA** | `Add Contact` |
| **Destination** | Tenant list filtered to missing contact (filter_code: 305) |
| **Icon** | `contact_missing.png` |
| **Urgency Text** | None |
| **Feature Gate** | None |
| **Status** | New |

---

---

## C. COMPLIANCE

Tasks under the `Compliance` category chip in View All. KYC, agreements, police verification, legal requirements.

---

### C1. Agreement Renewals Due

| Field | Detail |
| --- | --- |
| **Task Name** | Agreement Renewals Due |
| **Source Type** | Time-triggered |
| **Section** | Compliance |
| **Meaning** | Active tenants whose agreement end date has passed and needs renewal. Uses a multi-step date resolution that checks renewal history before falling back to joining date. |
| **Operator Description** | "How many tenants have agreements that have already expired and need renewal?" The system first checks for a renewal record (`tenant_agreement_renewals`), then uses `last_agreement_renewal_date`, then falls back to `date_of_joining`. Short-term tenants are excluded. |
| **Calculation Logic** | `COUNT(tenant) WHERE status = 1 AND is_short_term IS NOT TRUE AND (agreement_period IS NOT NULL OR property.agreement_period IS NOT NULL)`. End date resolution: **If** `last_agreement_renewal_date` is set → use `COALESCE(latest tenant_agreement_renewals.agreement_end_date, last_agreement_renewal_date + COALESCE(tenant.agreement_period, property.agreement_period, 11) months - 1 day)`. **Else** → `date_of_joining + COALESCE(tenant.agreement_period, property.agreement_period, 11) months - 1 day`. Task surfaces when computed end date < CURRENT_DATE. |
| **Source** | `service.ts:1611` (new system — `getPendingTasks()`, task `agreement_renewals_overdue`; SQL ~1426), `getAllTenants.ts:2698` (old system — `quickFilter()` widget "Agreement to renew this month", filter_code: 5005) |
| **Visibility** | Always shown when count > 0. Dismissible with midnight reset. |
| **Priority Color** | Orange |
| **Priority Tier** | T3 |
| **Sort Signal** | `days_overdue` |
| **Promotes To** | T2 when any agreement is overdue by > 30 days past expiry |
| **Required Access** | `view_tenants` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | Yes — midnight reset |
| **Title Pattern** | `{N} Agreement Renewal(s) Due` |
| **Subtitle** | Avoid lapses — renew expired agreements |
| **CTA** | `Renew Now` |
| **Destination** | Agreement renewal worklist |
| **Icon** | `agreement.png` |
| **Urgency Text** | None |
| **Feature Gate** | None |
| **Status** | Live (both systems) |

---

### C2. Renewed Agreement Not Signed

| Field | Detail |
| --- | --- |
| **Task Name** | Renewed Agreement Not Signed |
| **Source Type** | System-detected |
| **Section** | Compliance |
| **Meaning** | Tenants who have a renewal record in `tenant_agreement_renewals` but haven't signed the new agreement |
| **Operator Description** | "How many tenants have a renewal generated but haven't signed it?" The agreement was renewed on the system but the tenant hasn't signed the new copy. Unsigned renewals have no legal standing. |
| **Calculation Logic** | `COUNT(DISTINCT tenant) WHERE status = 1 AND EXISTS(tenant_agreement_renewals record)` |
| **Source** | `getAllTenants.ts:2747` (old system — `quickFilter()` widget "Pending Renewed Agreement not signed", filter_code: 8980) |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Red |
| **Priority Tier** | T2 |
| **Sort Signal** | `count` |
| **Promotes To** | — |
| **Required Access** | `view_tenants` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | Yes — midnight reset |
| **Title Pattern** | `{N} Renewed Agreement(s) Unsigned` |
| **Subtitle** | Renewals generated but not signed — no legal standing until signed |
| **CTA** | `Get Signed` |
| **Destination** | Tenant list filtered to unsigned renewals |
| **Icon** | `rental_agreement.png` |
| **Urgency Text** | None |
| **Feature Gate** | None |
| **Status** | Live (old system) — needs migration to new homepage |

---

### C3. Tenants to Sign Agreement

| Field | Detail |
| --- | --- |
| **Task Name** | Tenants to Sign Agreement |
| **Source Type** | System-detected |
| **Section** | Compliance |
| **Meaning** | Active tenants who don't have a rental agreement uploaded |
| **Operator Description** | "How many tenants are living here without a signed rental agreement?" No agreement URL on file means unsigned. This is a legal gap — in a dispute, you have no documentation. |
| **Calculation Logic** | `COUNT(DISTINCT tenant) WHERE status = 1 AND rental_agreement_url IS NULL` |
| **Source** | `getAllTenants.ts:2712` (old system — `quickFilter()` widget "Tenants to sign agreement", filter_code: 200) |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Red |
| **Priority Tier** | T2 |
| **Sort Signal** | `count` |
| **Promotes To** | — |
| **Required Access** | `view_tenants` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | Yes — midnight reset |
| **Title Pattern** | `{N} Tenant(s) Without Agreement` |
| **Subtitle** | No signed agreement on file — legal risk |
| **CTA** | `Get Signed` |
| **Destination** | Tenant list filtered to unsigned agreements |
| **Icon** | `rental_agreement.png` |
| **Urgency Text** | None |
| **Feature Gate** | None |
| **Status** | Live (old system) — needs migration to new homepage |

---

### C4. Bookings to Sign Agreement

| Field | Detail |
| --- | --- |
| **Task Name** | Bookings to Sign Agreement |
| **Source Type** | System-detected |
| **Section** | Compliance |
| **Meaning** | Booked tenants who don't have a rental agreement uploaded |
| **Operator Description** | "How many upcoming tenants haven't signed their rental agreement?" Get agreements signed before move-in so you're not scrambling on day one. |
| **Calculation Logic** | `COUNT(DISTINCT tenant) WHERE status = 2 AND rental_agreement_url IS NULL` |
| **Source** | `getAllTenants.ts:2975` (old system — booking widget (`booking_qb`, `status = 2`) "Bookings to sign agreement", filter_code: 200) |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Orange |
| **Priority Tier** | T3 |
| **Sort Signal** | `count` |
| **Promotes To** | — |
| **Required Access** | `view_tenants` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | Yes — midnight reset |
| **Title Pattern** | `{N} Booking(s) Without Agreement` |
| **Subtitle** | Get agreements signed before move-in |
| **CTA** | `Get Signed` |
| **Destination** | Booking list filtered to unsigned agreements |
| **Icon** | `rental_agreement.png` |
| **Urgency Text** | None |
| **Feature Gate** | None |
| **Status** | Live (old system) — needs migration to new homepage |

---

### C5. Tenants to Complete KYC

| Field | Detail |
| --- | --- |
| **Task Name** | Tenants to Complete KYC |
| **Source Type** | System-detected |
| **Section** | Compliance |
| **Meaning** | Active tenants whose Aadhaar/ID has not been verified |
| **Operator Description** | "How many tenants haven't completed their ID verification yet?" KYC is a compliance requirement — unverified tenants are a regulatory risk, especially during police checks. |
| **Calculation Logic** | `COUNT(DISTINCT tenant) WHERE status = 1 AND is_aadhar_verified = false` |
| **Source** | `getAllTenants.ts:2705` (old system — `quickFilter()` widget "Tenants to complete KYC Pending", filter_code: 201) |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Orange |
| **Priority Tier** | T3 |
| **Sort Signal** | `count` |
| **Promotes To** | T2 when police verification deadline within 7 days |
| **Required Access** | `view_tenants` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | Yes — midnight reset |
| **Title Pattern** | `{N} Tenant(s) KYC Pending` |
| **Subtitle** | Complete ID verification for compliance |
| **CTA** | `Verify Now` |
| **Destination** | Tenant list filtered to KYC pending |
| **Icon** | `kyc.png` |
| **Urgency Text** | None |
| **Feature Gate** | None |
| **Status** | Live (old system) — needs migration to new homepage |

---

### C6. Bookings to Complete KYC

| Field | Detail |
| --- | --- |
| **Task Name** | Bookings to Complete KYC |
| **Source Type** | System-detected |
| **Section** | Compliance |
| **Meaning** | Booked tenants whose Aadhaar/ID hasn't been verified |
| **Operator Description** | "How many upcoming tenants still need to verify their ID?" Get KYC done before move-in so you're not scrambling later. |
| **Calculation Logic** | `COUNT(DISTINCT tenant) WHERE status = 2 AND is_aadhar_verified = false` |
| **Source** | `getAllTenants.ts:2968` (old system — booking widget (`booking_qb`, `status = 2`) "Bookings to complete KYC", filter_code: 201) |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Orange |
| **Priority Tier** | T3 |
| **Sort Signal** | `count` |
| **Promotes To** | — |
| **Required Access** | `view_tenants` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | Yes — midnight reset |
| **Title Pattern** | `{N} Booking(s) KYC Pending` |
| **Subtitle** | Verify IDs before move-in |
| **CTA** | `Verify Now` |
| **Destination** | Booking list filtered to KYC pending |
| **Icon** | `kyc.png` |
| **Urgency Text** | None |
| **Feature Gate** | None |
| **Status** | Live (old system) — needs migration to new homepage |

---

### C7. Police Verifications to Complete

| Field | Detail |
| --- | --- |
| **Task Name** | Police Verifications to Complete |
| **Source Type** | System-detected |
| **Section** | Compliance |
| **Meaning** | Active tenants without a police verification document uploaded |
| **Operator Description** | "How many tenants don't have their police verification done?" Required by law in many states. Missing this puts you at legal risk during inspections. |
| **Calculation Logic** | `COUNT(DISTINCT tenant) WHERE status = 1 AND police_verification_url IS NULL` |
| **Source** | `getAllTenants.ts:2726` (old system — `quickFilter()` widget "Police verifications to complete", filter_code: 5009) |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Orange |
| **Priority Tier** | T3 |
| **Sort Signal** | `count` |
| **Promotes To** | — |
| **Required Access** | `view_tenants` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | Yes — midnight reset |
| **Title Pattern** | `{N} Police Verification(s) Pending` |
| **Subtitle** | Legal requirement — submit before the next inspection |
| **CTA** | `Upload Now` |
| **Destination** | Tenant list filtered to police verification pending |
| **Icon** | `police.png` |
| **Urgency Text** | None |
| **Feature Gate** | None |
| **Status** | Live (old system) — needs migration to new homepage |

---

### C8. KYC Credits Running Low

| Field | Detail |
| --- | --- |
| **Task Name** | KYC Credits Low |
| **Source Type** | System-detected |
| **Section** | Compliance |
| **Meaning** | InstaVeritas verification credits are running low (below threshold) |
| **Operator Description** | "Are you running out of KYC verification credits?" When credits hit zero, you can't verify new tenants. Recharge before you get stuck during a busy move-in period. |
| **Calculation Logic** | `SUM(instaveritas_credits.credits_alloted) - COUNT(instaveritas_usage) < 10` per property |
| **Source** | New — not yet implemented. Entities: `instaveritas_credits`, `instaveritas_usage`. |
| **Visibility** | Only shown when balance < threshold (e.g., 10 credits) |
| **Priority Color** | Orange |
| **Priority Tier** | T3 |
| **Sort Signal** | `count` |
| **Promotes To** | T2 when credits = 0 |
| **Required Access** | `view_tenants` |
| **Access Logic** | ANY |
| **Aggregation** | `any` |
| **Dismissible** | Yes — midnight reset |
| **Title Pattern** | `KYC Credits Running Low` |
| **Subtitle** | Only {N} credits left — recharge to keep verifying |
| **CTA** | `Recharge Now` |
| **Destination** | KYC credits recharge screen |
| **Icon** | `kyc_credits.png` |
| **Urgency Text** | `{N} credits remaining` |
| **Feature Gate** | KYC verification enabled for property |
| **Status** | New |

---

### C9. E-Sign Agreement Pending

| Field | Detail |
| --- | --- |
| **Task Name** | E-Sign Pending |
| **Source Type** | System-detected |
| **Section** | Compliance |
| **Meaning** | Digital agreements sent for e-signature but not yet signed by the tenant or team member |
| **Operator Description** | "How many e-sign agreements are waiting for signatures?" The agreement was sent digitally but the other party hasn't signed yet. Follow up — unsigned agreements have no legal standing. |
| **Calculation Logic** | `COUNT(*) FROM tenant_agreement_state WHERE status = 'pending' AND is_active = true AND signed_at IS NULL` |
| **Source** | New — entity: `tenantAgreementState.ts` with `status` (default 'pending'), `party_type` (1=tenant, 2=team member), `signed_at` nullable. |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Orange |
| **Priority Tier** | T3 |
| **Sort Signal** | `count` |
| **Promotes To** | — |
| **Required Access** | `view_tenants` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | Yes — midnight reset |
| **Title Pattern** | `{N} E-Sign(s) Pending` |
| **Subtitle** | Digital agreements waiting for signature — follow up |
| **CTA** | `Follow Up` |
| **Destination** | E-sign agreements list filtered to pending |
| **Icon** | `esign.png` |
| **Urgency Text** | None |
| **Feature Gate** | E-sign feature enabled |
| **Status** | New |

---

---

## D. PROPERTY

Tasks under the `Property` category chip in View All. Complaints, inspections, checklists, physical operations.

---

### D1. Complaints Unassigned

| Field | Detail |
| --- | --- |
| **Task Name** | Complaints Unassigned |
| **Source Type** | Event-driven |
| **Section** | Property |
| **Meaning** | Complaints raised by tenants with no staff member assigned |
| **Operator Description** | "How many complaints don't have anyone assigned to handle them?" Unassigned complaints = nobody is working on them. Assign to a team member before the tenant follows up or escalates. |
| **Calculation Logic** | `COUNT(*) FROM complaints WHERE property_id = ANY(...) AND team_member_id IS NULL AND status != 5 (resolved)`. Note: live code at `service.ts:1463` only excludes status 5 (resolved). Escalation code (`escalateComplaint.ts`) also uses only `status != 5` / `Not(5)` — no code path excludes status 10 today; adding `AND status != 10` remains a consistency suggestion only. |
| **Source** | `service.ts:1460` (query), rendered as task `unassigned_complaints` at `service.ts:1554–1563` (new system, block-based v2 feed) |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Red |
| **Priority Tier** | T2 |
| **Sort Signal** | `count` |
| **Promotes To** | T1 when any unassigned complaint > 24 hours old |
| **Required Access** | `view_complaint` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Assignee Source** | `ComplaintResponderMap` — uses the complaint category → designated responder mapping, not just anyone with `view_complaint` |
| **Dismissible** | No |
| **Title Pattern** | `{N} Complaint(s) Unassigned` |
| **Subtitle** | Assign before tenants follow up |
| **CTA** | `Assign Now` |
| **Destination** | Complaints list filtered to unassigned |
| **Icon** | `complaint_unassigned.png` |
| **Urgency Text** | None |
| **Feature Gate** | None |
| **Status** | Live (new system) |

---

### D2. Complaints Overdue

| Field | Detail |
| --- | --- |
| **Task Name** | Complaints Overdue |
| **Source Type** | System-detected |
| **Section** | Property |
| **Meaning** | Complaints that have passed their expected resolution date |
| **Operator Description** | "How many complaints are past their expected resolution date?" These were supposed to be fixed by now. Tenants are likely frustrated — escalate or close them. |
| **Calculation Logic** | `COUNT(*) FROM complaints WHERE status NOT IN (5, 10) AND expected_resolution_date IS NOT NULL AND expected_resolution_date < CURRENT_DATE` |
| **Source** | New — not yet implemented. Column `expected_resolution_date` exists on complaints entity. |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Red |
| **Priority Tier** | T1 |
| **Sort Signal** | `days_overdue` |
| **Promotes To** | — |
| **Required Access** | `view_complaint, edit_complaint` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Assignee Source** | Complaint's assigned `team_member_id` — the person already working on it |
| **Dismissible** | No |
| **Title Pattern** | `{N} Complaint(s) Overdue` |
| **Subtitle** | Past resolution date — escalate or resolve now |
| **CTA** | `Resolve Now` |
| **Destination** | Complaints list filtered to overdue |
| **Icon** | `complaint_overdue.png` |
| **Urgency Text** | `Past deadline` |
| **Feature Gate** | None |
| **Status** | New |

---

### D3. Inspections Pending

| Field | Detail |
| --- | --- |
| **Task Name** | Inspections Pending |
| **Source Type** | Time-triggered |
| **Section** | Property |
| **Meaning** | Room/property inspections scheduled for today but not yet completed |
| **Operator Description** | "How many inspections are pending today?" Your team hasn't submitted their inspection results. Mark complete or reschedule — don't let them pile up. |
| **Calculation Logic** | `COUNT(*) FROM task_instance JOIN task_schedule WHERE task_instance.status = 'pending' AND DATE(task_instance.created_at) = CURRENT_DATE` |
| **Source** | Query at `service.ts:1470–1478` (computes `inspectionsRows`), but the task-render block is COMMENTED OUT / dead at `service.ts:1625–1634` — the count is fetched yet never surfaced. NOT live. |
| **Visibility** | Always shown when count > 0. Dismissible with midnight reset. |
| **Priority Color** | Orange |
| **Priority Tier** | T3 |
| **Sort Signal** | `count` |
| **Promotes To** | — |
| **Required Access** | `view_room` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | Yes — midnight reset |
| **Title Pattern** | `{N} Inspection(s) Pending` |
| **Subtitle** | Mark complete or reschedule |
| **CTA** | `View Inspections` |
| **Destination** | Inspection list filtered to pending |
| **Icon** | `inspection.png` |
| **Urgency Text** | `Today` |
| **Feature Gate** | None |
| **Status** | NOT live — the `inspection_pending` render block is commented-out dead code in `getPendingTasks()` (the query runs and the count is fetched, but never surfaced); see Source. |

---

### D4. Inspections Expired

| Field | Detail |
| --- | --- |
| **Task Name** | Inspections Expired |
| **Source Type** | System-detected |
| **Section** | Property |
| **Meaning** | Inspections that were never completed and have expired |
| **Operator Description** | "How many inspections were missed entirely?" These were scheduled but nobody submitted. Either reschedule or mark them — leaving expired inspections piling up means your cleaning/inspection process is broken. |
| **Calculation Logic** | `COUNT(*) FROM task_instance WHERE status = 'expired'` |
| **Source** | New — not yet implemented. Note: `task_instance` is a raw table (queried via raw SQL in `service.ts`), not a TypeORM entity — no `task_instance` entity file exists, so the `'expired'` status enum could not be verified from an entity definition. |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Orange |
| **Priority Tier** | T3 |
| **Sort Signal** | `count` |
| **Promotes To** | — |
| **Required Access** | `view_room` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | Yes — midnight reset |
| **Title Pattern** | `{N} Inspection(s) Expired` |
| **Subtitle** | Missed inspections — reschedule or review |
| **CTA** | `Review Now` |
| **Destination** | Inspection list filtered to expired |
| **Icon** | `inspection_expired.png` |
| **Urgency Text** | `Missed` |
| **Feature Gate** | None |
| **Status** | New |

---

### D5. Move-In Checklist Pending

| Field | Detail |
| --- | --- |
| **Task Name** | Move-In Verifications Pending |
| **Source Type** | Event-driven |
| **Section** | Property |
| **Meaning** | Tenants who have submitted their move-in checklist but it hasn't been verified by the manager |
| **Operator Description** | "How many tenants have move-in checklists waiting for your review?" The checklist documents room condition at move-in. Verify it promptly — if there's a dispute at move-out, this is your evidence. |
| **Calculation Logic** | New system: `COUNT(*) FROM tenant_checklist WHERE checklist_type = 'move_in' AND status = 1`. Old system (broader): `checklist IS NULL OR status < 2 OR status = 3`. |
| **Source** | New system: query at `service.ts:1404–1411`, rendered as task `move_in_checklist_pending` at `service.ts:1600` (category is `People` in code, not `Property`). Old system: `src/services/property/getAllTenants.ts` (moved from `src/controllers/`); line 2355 and filter_codes 5010/2384 not found on current master — unverified/stale. |
| **Visibility** | Always shown when count > 0. Dismissible with midnight reset. |
| **Priority Color** | Orange |
| **Priority Tier** | T2 |
| **Sort Signal** | `count` |
| **Promotes To** | — |
| **Required Access** | `submit_or_update_moveout_checklist_access` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | Yes — midnight reset |
| **Title Pattern** | `{N} Move-In Verification(s) Pending` |
| **Subtitle** | Verify tenant documents |
| **CTA** | `Verify Now` |
| **Destination** | KYC/document verification worklist |
| **Icon** | `checklist.png` |
| **Urgency Text** | None |
| **Feature Gate** | `movein_moveout_checklist_enabled = 1` |
| **Status** | Live (both systems) |

---

### D6. Move-Out Checklist Not Locked

| Field | Detail |
| --- | --- |
| **Task Name** | Move-Out Checklist Not Locked |
| **Source Type** | Event-driven |
| **Section** | Property |
| **Meaning** | Move-out checklists in progress but not finalized |
| **Operator Description** | "How many move-out checklists are still open and editable?" Until locked, the damage assessment isn't official. Lock them before processing the security deposit refund. |
| **Calculation Logic** | `COUNT(DISTINCT tenant) WHERE status = 1 AND movein_moveout_checklist_enabled = 1 AND checklist IS NOT NULL AND checklist.status >= 4 AND checklist.status < 6` |
| **Source** | `src/services/property/getAllTenants.ts` (moved from `src/controllers/`); line 2366 and filter_code 2385 not found on current master — unverified/stale, likely drifted. |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Orange |
| **Priority Tier** | T2 |
| **Sort Signal** | `count` |
| **Promotes To** | — |
| **Required Access** | `approve_moveout_checklist_access` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | Yes — midnight reset |
| **Title Pattern** | `{N} Move-Out Checklist(s) Not Locked` |
| **Subtitle** | Lock before processing deposit refund |
| **CTA** | `Lock Now` |
| **Destination** | Move-out checklist worklist |
| **Icon** | `checklist.png` |
| **Urgency Text** | None |
| **Feature Gate** | `movein_moveout_checklist_enabled = 1` |
| **Status** | Live (old system) — needs migration to new homepage |

---

### D7. Meter Readings Due

| Field | Detail |
| --- | --- |
| **Task Name** | Meter Readings Due |
| **Source Type** | Time-triggered |
| **Section** | Property |
| **Meaning** | Active meters that don't have a reading recorded for the current billing cycle |
| **Operator Description** | "Have all meter readings been recorded this month?" Missing readings mean estimated bills — tenants dispute estimates. Record them before generating this cycle's invoices. |
| **Calculation Logic** | `COUNT(*) FROM meters WHERE is_active = 1 AND NOT EXISTS(meter_historical_reading for current billing cycle)` |
| **Source** | New — not yet implemented. Entities: `meters`, `meter_historical_reading`. |
| **Visibility** | Shown when count > 0, typically in the last week of a billing cycle |
| **Priority Color** | Orange |
| **Priority Tier** | T3 |
| **Sort Signal** | `deadline` |
| **Promotes To** | — |
| **Required Access** | `edit_electricity_meter_status` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | Yes — midnight reset |
| **Title Pattern** | `{N} Meter Reading(s) Due` |
| **Subtitle** | Record before generating this cycle's bills |
| **CTA** | `Record Now` |
| **Destination** | Meter readings screen |
| **Icon** | `meter.png` |
| **Urgency Text** | `This cycle` |
| **Feature Gate** | Electricity/meter billing enabled |
| **Status** | New |

---

### D8. Escalated Complaints — L2

| Field | Detail |
| --- | --- |
| **Task Name** | Complaints Escalated (L2) |
| **Source Type** | System-detected |
| **Section** | Property |
| **Meaning** | Complaints unresolved for 48+ hours that have been auto-escalated to L2 |
| **Operator Description** | "Which complaints have been open long enough to escalate?" These have hit the 48-hour mark without resolution. The system has already reassigned them — but the manager needs to ensure they're being actively worked. |
| **Calculation Logic** | `COUNT(*) FROM complaints WHERE status NOT IN (5, 10) AND created_at BETWEEN (NOW() - l3_time hours) AND (NOW() - l2_time hours)`. Default: complaints open 48–72 hours. Escalation times are configurable per property per complaint category via `ComplaintEscalationTime` (defaults: L1=24h, L2=48h, L3=72h). Note: the escalation cron in `escalateComplaint.ts` uses a narrow 1-hour window (`Between(now - (l2+1)h, now - l2h)`) to trigger notifications — the pending task should use the full L2 range (l2 to l3) for counting. |
| **Source** | New — L2 escalation logic in `src/services/complaints/escalateComplaint.ts:113–121` (`Between` window at line 117); a newer parallel implementation exists in `escalate_v2.ts` (uses `effective_l2_tat` + `isAfter`, no `Between` window). Not surfaced as a pending task. |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Orange |
| **Priority Tier** | T3 |
| **Sort Signal** | `days_overdue` |
| **Promotes To** | T2 when any L2 complaint approaching L3 threshold |
| **Required Access** | `view_complaint, edit_complaint` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Assignee Source** | Escalation responder from `ComplaintResponderMap` — the L2 assignee |
| **Dismissible** | No |
| **Title Pattern** | `{N} Complaint(s) Escalated` |
| **Subtitle** | 48+ hours unresolved — intervention needed |
| **CTA** | `Resolve Now` |
| **Destination** | Complaints list filtered to escalated L2 |
| **Icon** | `complaint_escalated.png` |
| **Urgency Text** | `48+ hours` |
| **Feature Gate** | None |
| **Status** | New |

---

### D9. Escalated Complaints — L3

| Field | Detail |
| --- | --- |
| **Task Name** | Complaints Critical (L3) |
| **Source Type** | System-detected |
| **Section** | Property |
| **Meaning** | Complaints unresolved for 72+ hours that have been auto-escalated to L3 (highest level) |
| **Operator Description** | "Which complaints have reached critical escalation?" These have been open 3+ days. The escalation system has exhausted its levels — this needs direct owner/admin attention. |
| **Calculation Logic** | `COUNT(*) FROM complaints WHERE status NOT IN (5, 10) AND created_at < (NOW() - l3_time hours)`. Default: complaints open 72+ hours. Configurable per property per complaint category via `ComplaintEscalationTime` (default L3=72h). Note: the escalation cron uses a 100-hour window (`Between(now - (l3+100)h, now - l3h)`) to catch complaints up to 172h old — complaints older than that are no longer actively escalated by the cron but should still appear in the pending task count. |
| **Source** | New — L3 escalation logic in `src/services/complaints/escalateComplaint.ts:137–145` (`Between` window at line 140); newer parallel implementation in `escalate_v2.ts` (uses `effective_l3_tat`). Not surfaced as a pending task. |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Red |
| **Priority Tier** | T1 |
| **Sort Signal** | `days_overdue` |
| **Promotes To** | — (already T1) |
| **Required Access** | `view_complaint, edit_complaint` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Assignee Source** | Escalation authority from `ComplaintResponderMap` — the L3 assignee |
| **Dismissible** | No |
| **Title Pattern** | `{N} Complaint(s) Critical` |
| **Subtitle** | 72+ hours unresolved — owner/admin attention required |
| **CTA** | `Resolve Now` |
| **Destination** | Complaints list filtered to L3 escalated |
| **Icon** | `complaint_critical.png` |
| **Urgency Text** | `72+ hours — SLA breach` |
| **Feature Gate** | None |
| **Status** | New |

---

### D10. Overbooked Rooms

| Field | Detail |
| --- | --- |
| **Task Name** | Rooms Overbooked |
| **Source Type** | System-detected |
| **Section** | Property |
| **Meaning** | Rooms where active tenant count exceeds the room's sharing capacity |
| **Operator Description** | "Are any rooms over capacity?" More tenants are assigned than beds available. This means either a data error or an actual double-booking. Either way, it needs immediate fixing. |
| **Calculation Logic** | `COUNT(*) FROM rooms WHERE active_tenant_count > sharing_type` — sharing_type defines room capacity |
| **Source** | `controllers/rooms.ts:1724` (detection — `sharing_type < roomActiveTenantCount` sets `is_overbook_rooms`), `v1/constants/filterCodes.ts:151-152` (OVERBOOKED=1413, OVERBOOKED_BEDS=1414) |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Orange |
| **Priority Tier** | T3 |
| **Sort Signal** | `count` |
| **Promotes To** | T2 when an overbooked room has a new tenant move-in within 7 days |
| **Required Access** | `view_room` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | No |
| **Title Pattern** | `{N} Room(s) Overbooked` |
| **Subtitle** | More tenants than beds — reassign or add capacity |
| **CTA** | `Fix Now` |
| **Destination** | Room list filtered to overbooked (filter_code: 1413) |
| **Icon** | `overbooked.png` |
| **Urgency Text** | `Over capacity` |
| **Feature Gate** | None |
| **Status** | New |

---

### D11. Asset Warranty Expiring

| Field | Detail |
| --- | --- |
| **Task Name** | Asset Warranties Expiring |
| **Source Type** | System-detected |
| **Section** | Property |
| **Meaning** | Inventoried assets with warranty expiring within 30 days |
| **Operator Description** | "Are any asset warranties about to expire?" File claims or arrange repairs before the warranty runs out. After expiry, you pay out of pocket. |
| **Calculation Logic** | Columns must be computed, not read: `inventory` has NO `warranty_status` column and NO `warranty_end_date`. Actual columns are `warranty_expiry_date` (date) and a single `status` column holding `AssetStatus` (not `WarrantyStatus`). `WarrantyStatus.EXPIRING_SOON` is an enum only — never persisted. Correct logic: `COUNT(*) FROM inventory WHERE is_active = true AND warranty_expiry_date BETWEEN CURRENT_DATE AND (CURRENT_DATE + INTERVAL '30 days')`. |
| **Source** | New — entity: `inventory.ts` with `AssetStatus` and `WarrantyStatus` enums including `EXPIRING_SOON`. |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Orange |
| **Priority Tier** | T4 |
| **Sort Signal** | `deadline` |
| **Promotes To** | — |
| **Required Access** | `view_room` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | Yes — midnight reset |
| **Title Pattern** | `{N} Asset Warranty(s) Expiring` |
| **Subtitle** | File claims before warranty expires |
| **CTA** | `Review Assets` |
| **Destination** | Asset list filtered to expiring warranty |
| **Icon** | `warranty.png` |
| **Urgency Text** | `Within 30 days` |
| **Feature Gate** | Asset/inventory management enabled |
| **Status** | New |

---

---

## E. DAILY OPS

Time-triggered routine tasks and operational workflows that keep the PG running day-to-day.

---

### E1. Update Manager App

| Field | Detail |
| --- | --- |
| **Task Name** | New Update Available |
| **Source Type** | System-detected |
| **Section** | Daily Ops |
| **Meaning** | Current app version is behind the latest release |
| **Operator Description** | "Is there a newer version of the Manager App?" Update to get the latest features and bug fixes. |
| **Calculation Logic** | `internal_config` key='latest_build_number', compare with client's `x-build-number` header. Platform-specific (Android vs iOS). |
| **Source** | `service.ts:1565` (new system) |
| **Visibility** | Shown when client build < latest build |
| **Priority Color** | Blue |
| **Priority Tier** | T5 |
| **Sort Signal** | `static` |
| **Promotes To** | — |
| **Required Access** | `—` (universal) |
| **Access Logic** | ANY |
| **Aggregation** | `any` |
| **Dismissible** | No |
| **Title Pattern** | `New Update Available` |
| **Subtitle** | Get the latest features and fixes |
| **CTA** | `Update Now` |
| **Destination** | App store / in-app update flow |
| **Icon** | `app_update.png` |
| **Urgency Text** | None |
| **Feature Gate** | None |
| **Status** | Live (new system) |

---

### E2. Review Food Menu

| Field | Detail |
| --- | --- |
| **Task Name** | Update Food Menu |
| **Source Type** | Time-triggered |
| **Section** | Daily Ops |
| **Meaning** | Weekly reminder to review food menu (Monday only, food-enabled properties) |
| **Operator Description** | "Have you updated this week's food menu?" Tenants check the menu to decide meals. An outdated menu causes confusion and food waste. |
| **Calculation Logic** | Day-of-week = Monday AND property has `food_attendance_enabled = 1`. Don't resurface if user updated menu from this card in the current week. |
| **Source** | `service.ts:1637` (new system) |
| **Visibility** | Mondays only, food-enabled properties. Dismissible with weekly Monday reset. |
| **Priority Color** | Blue |
| **Priority Tier** | T5 |
| **Sort Signal** | `static` |
| **Promotes To** | — |
| **Required Access** | `view_food` |
| **Access Logic** | ANY |
| **Aggregation** | `any` |
| **Dismissible** | Yes — weekly Monday reset |
| **Title Pattern** | `Update Food Menu` |
| **Subtitle** | It's been a week — review your menu and timings |
| **CTA** | `Update Menu` |
| **Destination** | Food menu management screen |
| **Icon** | `food_menu.png` |
| **Urgency Text** | None |
| **Feature Gate** | `food_attendance_enabled = 1` |
| **Status** | Live (new system) |

---

### E3. Review Expenses

| Field | Detail |
| --- | --- |
| **Task Name** | Pending Expenses |
| **Source Type** | Time-triggered |
| **Section** | Daily Ops |
| **Meaning** | Saturday reminder to log the week's expenses |
| **Operator Description** | "Have you logged all expenses for the week?" Expenses recorded in real time are accurate. A week later, you'll forget the small ones — and those add up. |
| **Calculation Logic** | Day-of-week = Saturday. Always count = 1. |
| **Source** | `service.ts:1649` (new system) |
| **Visibility** | Saturdays only. Dismissible with weekly Saturday reset. |
| **Priority Color** | Blue |
| **Priority Tier** | T5 |
| **Sort Signal** | `static` |
| **Promotes To** | — |
| **Required Access** | `view_expenses` |
| **Access Logic** | ANY |
| **Aggregation** | `any` |
| **Dismissible** | Yes — weekly Saturday reset |
| **Title Pattern** | `Pending Expenses` |
| **Subtitle** | Week's ending — don't forget to log expenses |
| **CTA** | `Add Expenses` |
| **Destination** | Expense entry screen |
| **Icon** | `expense.png` |
| **Urgency Text** | None |
| **Feature Gate** | None |
| **Status** | Live (new system) |

---

### E4. Watch Latest Tutorial

| Field | Detail |
| --- | --- |
| **Task Name** | New Tutorial Available |
| **Source Type** | Time-triggered |
| **Section** | Daily Ops |
| **Meaning** | A new tutorial video was published within the last 7 days |
| **Operator Description** | "There's a new tutorial showing you a feature or workflow." Watch it to get more out of the app. |
| **Calculation Logic** | `internal_config` key='homepage_tutorials', filter videos where `published_at` is within last 7 days. |
| **Source** | `service.ts:1662` (new system) |
| **Visibility** | For 7 days after a new tutorial is published. Dismissible permanently (after first tap). |
| **Priority Color** | Blue |
| **Priority Tier** | T5 |
| **Sort Signal** | `static` |
| **Promotes To** | — |
| **Required Access** | `—` (universal) |
| **Access Logic** | ANY |
| **Aggregation** | `any` |
| **Dismissible** | Yes — permanent (after first view) |
| **Title Pattern** | `New Tutorial Available` |
| **Subtitle** | See what's new in the Manager App |
| **CTA** | `Watch Now` |
| **Destination** | Tutorial / video screen |
| **Icon** | `tutorial.png` |
| **Urgency Text** | None |
| **Feature Gate** | None |
| **Status** | Live (new system) |

---

### E5. Exit Requests Pending

| Field | Detail |
| --- | --- |
| **Task Name** | Exit Requests Pending |
| **Source Type** | Event-driven |
| **Section** | Daily Ops |
| **Meaning** | Tenants waiting for exit/leave approval via the entry-exit system |
| **Operator Description** | "How many tenants are waiting for exit approval?" Time-sensitive — they may be physically waiting at the gate. |
| **Calculation Logic** | `COUNT(*) FROM entry_exit_request WHERE request_type = 'EXIT' AND request_status = 'PENDING'` |
| **Source** | Entity: `entryExitRequests.ts` with request_type and request_status fields. `entryExitService.ts:2071`. |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Red |
| **Priority Tier** | T1 |
| **Sort Signal** | `count` |
| **Promotes To** | — |
| **Required Access** | `view_tenants` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | No |
| **Title Pattern** | `{N} Exit Request(s) Pending` |
| **Subtitle** | Tenants waiting at the gate — don't keep them waiting |
| **CTA** | `Approve Now` |
| **Destination** | Entry/exit requests list filtered to exit |
| **Icon** | `entry_exit.png` |
| **Urgency Text** | `Waiting` |
| **Feature Gate** | Entry/exit system enabled |
| **Status** | New |

---

### E6. Late Entry Requests Pending

| Field | Detail |
| --- | --- |
| **Task Name** | Late Entry Requests |
| **Source Type** | Event-driven |
| **Section** | Daily Ops |
| **Meaning** | Tenants requesting late entry back into the property |
| **Operator Description** | "How many tenants have requested late entry?" They missed the regular check-in window and need approval to enter. Approve or deny based on your policy. |
| **Calculation Logic** | `COUNT(*) FROM entry_exit_request WHERE request_type = 'LATE ENTRY' AND request_status = 'PENDING'` |
| **Source** | Entity: `entryExitRequests.ts`. `entryExitService.ts:2055`. |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Orange |
| **Priority Tier** | T2 |
| **Sort Signal** | `count` |
| **Promotes To** | — |
| **Required Access** | `view_tenants` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | No |
| **Title Pattern** | `{N} Late Entry Request(s)` |
| **Subtitle** | Tenants requesting after-hours entry — review now |
| **CTA** | `Review Now` |
| **Destination** | Entry/exit requests list filtered to late entry |
| **Icon** | `late_entry.png` |
| **Urgency Text** | None |
| **Feature Gate** | Entry/exit system enabled |
| **Status** | New |

---

### E7. Late Check-In Requests Pending

| Field | Detail |
| --- | --- |
| **Task Name** | Late Check-In Requests |
| **Source Type** | Event-driven |
| **Section** | Daily Ops |
| **Meaning** | Attendance-based late check-in requests awaiting manager/warden/parent approval |
| **Operator Description** | "How many students have late check-in requests pending?" These are separate from entry/exit — they go through the attendance approval chain (warden → parent). |
| **Calculation Logic** | `COUNT(*) FROM tenant_attendance_pending_requests WHERE type = 'late-checkin' AND is_active = 1 AND (warden_approval = 0 OR parent_approval = 0)`. Approval states: `NULL`=not applicable (auto-approved), `0`=pending, `1`=approved, `-1`=rejected. DB column defaults to NULL; application explicitly sets `0` when that approval step is required. |
| **Source** | Entity: parallel attendance system. `tenantAttendance.ts:782` for type check. |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Orange |
| **Priority Tier** | T2 |
| **Sort Signal** | `count` |
| **Promotes To** | — |
| **Required Access** | `view_tenants` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | No |
| **Title Pattern** | `{N} Late Check-In Request(s)` |
| **Subtitle** | Attendance requests awaiting approval |
| **CTA** | `Approve Now` |
| **Destination** | Attendance pending requests list |
| **Icon** | `attendance_request.png` |
| **Urgency Text** | None |
| **Feature Gate** | `is_attendance_enabled = 1` |
| **Status** | New |

---

### E8. Leave Requests Pending

| Field | Detail |
| --- | --- |
| **Task Name** | Leave Requests Pending |
| **Source Type** | Event-driven |
| **Section** | Daily Ops |
| **Meaning** | Attendance-based leave requests awaiting approval |
| **Operator Description** | "How many students have leave requests pending?" Leave requests go through the same approval chain as late check-ins (warden → parent). |
| **Calculation Logic** | `COUNT(*) FROM tenant_attendance_pending_requests WHERE type = 'leave' AND is_active = 1 AND (warden_approval = 0 OR parent_approval = 0)`. Approval states: `NULL`=not applicable, `0`=pending, `1`=approved, `-1`=rejected. Note: the `type` column stores lowercase values (`'leave'`, `'late-checkin'`), but the service also checks `'Leave'` at `tenantAttendance.ts:804` — query should be case-insensitive or match both. |
| **Source** | Entity: parallel attendance system. `tenantAttendance.ts:778` for type check. |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Orange |
| **Priority Tier** | T2 |
| **Sort Signal** | `count` |
| **Promotes To** | — |
| **Required Access** | `view_tenants` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | No |
| **Title Pattern** | `{N} Leave Request(s) Pending` |
| **Subtitle** | Students waiting for leave approval |
| **CTA** | `Approve Now` |
| **Destination** | Attendance pending requests list filtered to leave |
| **Icon** | `leave_request.png` |
| **Urgency Text** | None |
| **Feature Gate** | `is_attendance_enabled = 1` |
| **Status** | New |

---

### E9. Attendance Not Marked Today

| Field | Detail |
| --- | --- |
| **Task Name** | Attendance Not Marked Today |
| **Source Type** | Time-triggered |
| **Section** | Daily Ops |
| **Meaning** | Properties with attendance tracking enabled where no attendance has been recorded today |
| **Operator Description** | "Has today's attendance been marked?" If you track student or tenant attendance, a missed day means gaps in your records. |
| **Calculation Logic** | Properties where `is_attendance_enabled = 1` and no `tenant_attendance` records exist for today |
| **Source** | New — not yet implemented. Entity: `tenant_attendance`. Cron: `services/cron/attendance.ts` sends WhatsApp reminders but doesn't alert managers. |
| **Visibility** | Shown after a configurable cutoff time (e.g., 10 AM) if attendance hasn't been marked |
| **Priority Color** | Orange |
| **Priority Tier** | T3 |
| **Sort Signal** | `static` |
| **Promotes To** | — |
| **Required Access** | `view_tenants` |
| **Access Logic** | ANY |
| **Aggregation** | `any` |
| **Dismissible** | Yes — midnight reset |
| **Title Pattern** | `Attendance Not Marked Today` |
| **Subtitle** | Mark today's attendance before end of day |
| **CTA** | `Mark Now` |
| **Destination** | Attendance screen |
| **Icon** | `attendance.png` |
| **Urgency Text** | `Today` |
| **Feature Gate** | `is_attendance_enabled = 1` |
| **Status** | New |

---

### E10. Add-On Bookings to Confirm

| Field | Detail |
| --- | --- |
| **Task Name** | Add-On Bookings to Confirm |
| **Source Type** | Event-driven |
| **Section** | Daily Ops |
| **Meaning** | Add-on service bookings (laundry, meals, etc.) awaiting manager confirmation |
| **Operator Description** | "How many add-on service bookings are waiting for you to confirm?" Tenants booked a service — confirm so the vendor or team can prepare. |
| **Calculation Logic** | `COUNT(*) FROM addon_service_booking WHERE status = 'pending' AND property_id = ANY(...)` |
| **Source** | New — not yet implemented. Entity: `addon_service_booking` with `BookingStatus.PENDING`. |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Orange |
| **Priority Tier** | T2 |
| **Sort Signal** | `count` |
| **Promotes To** | — |
| **Required Access** | `view_tenants` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | Yes — midnight reset |
| **Title Pattern** | `{N} Add-On Booking(s) to Confirm` |
| **Subtitle** | Confirm so service can be prepared |
| **CTA** | `Confirm Now` |
| **Destination** | Add-on service bookings list |
| **Icon** | `addon_service.png` |
| **Urgency Text** | None |
| **Feature Gate** | Add-on services enabled for property |
| **Status** | New |

---

### E11. Team Salary Due

| Field | Detail |
| --- | --- |
| **Task Name** | Team Salary Due |
| **Source Type** | Time-triggered |
| **Section** | Daily Ops |
| **Meaning** | Staff salaries that are due but not yet paid |
| **Operator Description** | "Are any team member salaries due?" Pay your staff on time — delayed salaries affect morale and retention. |
| **Calculation Logic** | `COUNT(*) FROM team_salaries WHERE is_paid = 0 AND due_date <= CURRENT_DATE` |
| **Source** | New — not yet implemented. Entity: `team_salaries` with `is_paid` and `due_date` fields. |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Orange |
| **Priority Tier** | T2 |
| **Sort Signal** | `days_overdue` |
| **Promotes To** | — |
| **Required Access** | `view_team` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | Yes — midnight reset |
| **Title Pattern** | `{N} Team Salary(s) Due` |
| **Subtitle** | Pay your team on time |
| **CTA** | `Pay Now` |
| **Destination** | Team salaries screen |
| **Icon** | `team_salary.png` |
| **Urgency Text** | `Due` |
| **Feature Gate** | None |
| **Cross-Reference** | Same underlying data as A13 (Staff Salary & Reimbursement Due) in Money. E11 is the salary-only view under Daily Ops; A13 includes all payment types (salary + bonus + reimbursement) under Money. Implementation must deduplicate or differentiate scope. |
| **Status** | New |

---

---

## F. GROWTH

Tasks that drive occupancy, conversion, and tenant adoption. Not fixing problems — building the business.

---

### F1. Tenants to Set Up AutoPay

| Field | Detail |
| --- | --- |
| **Task Name** | Tenants to Set Up AutoPay |
| **Source Type** | System-detected |
| **Section** | Growth |
| **Meaning** | Active tenants who don't have an autopay mandate set up |
| **Operator Description** | "How many tenants haven't set up automatic rent payments?" Every tenant on autopay is one less person you chase every month. The more autopay, the less manual collection effort. |
| **Calculation Logic** | `COUNT(DISTINCT tenant) WHERE status = 1 AND autopay.id IS NULL` (LEFT JOIN on autopay table, no matching row) |
| **Source** | `getAllTenants.ts:2656` (old system, filter_code: 5011) — `autopay_pending_count` widget count via `.leftJoin('autopay'...)` `COUNT(... ap.id IS NULL)` at :2655-2656, filter_code 5011 emitted at :2744, applied at :4213 |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Orange |
| **Priority Tier** | T4 |
| **Sort Signal** | `count` |
| **Promotes To** | — |
| **Required Access** | `view_tenants` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | Yes — midnight reset |
| **Title Pattern** | `{N} Tenant(s) Without AutoPay` |
| **Subtitle** | More autopay = less chasing every month |
| **CTA** | `Set Up Now` |
| **Destination** | Tenant list filtered to no autopay |
| **Icon** | `autopay.png` |
| **Urgency Text** | None |
| **Feature Gate** | Property has autopay enabled |
| **Status** | Live (old system) — needs migration to new homepage |

---

### F2. Visits Scheduled Today

| Field | Detail |
| --- | --- |
| **Task Name** | Visits Scheduled Today |
| **Source Type** | Time-triggered |
| **Section** | Growth |
| **Meaning** | Property visits scheduled for today |
| **Operator Description** | "How many prospects are visiting today?" Prepare the rooms, common areas, and whoever is giving the tour. A good visit converts to a booking. |
| **Calculation Logic** | `COUNT(*) FROM visits WHERE visit_date = CURRENT_DATE AND status = 'SCHEDULED'` |
| **Source** | New — not yet implemented. Entity: `visits`. |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Green |
| **Priority Tier** | T3 |
| **Sort Signal** | `deadline` |
| **Promotes To** | — |
| **Required Access** | `view_leads` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | Yes — midnight reset |
| **Title Pattern** | `{N} Visit(s) Scheduled Today` |
| **Subtitle** | Prepare rooms for incoming prospects |
| **CTA** | `View Visits` |
| **Destination** | Today's visit schedule |
| **Icon** | `visit.png` |
| **Urgency Text** | `Today` |
| **Feature Gate** | None |
| **Status** | New |

---

### F3. Leads Without Follow-Up

| Field | Detail |
| --- | --- |
| **Task Name** | Leads Without Follow-Up |
| **Source Type** | System-detected |
| **Section** | Growth |
| **Meaning** | Leads older than 48 hours with no status update or follow-up logged |
| **Operator Description** | "How many leads have gone cold without a follow-up?" A lead older than 48 hours without contact is probably lost. Each stale lead is a potential booking that walked away. |
| **Calculation Logic** | `COUNT(DISTINCT tenant) WHERE status = 3 (lead) AND created_at < (NOW() - INTERVAL '48 hours') AND NOT EXISTS(lead_status WHERE tenant_id = t.id AND created_at > t.created_at)` |
| **Source** | New — not yet implemented. Entities: `tenant` (status=3), `lead_status`. |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Orange |
| **Priority Tier** | T4 |
| **Sort Signal** | `days_overdue` |
| **Promotes To** | — |
| **Required Access** | `view_leads` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | Yes — midnight reset |
| **Title Pattern** | `{N} Lead(s) Without Follow-Up` |
| **Subtitle** | Leads going cold — follow up before they're lost |
| **CTA** | `Follow Up` |
| **Destination** | Lead list filtered to stale (>48 hours) |
| **Icon** | `lead.png` |
| **Urgency Text** | `48+ hours` |
| **Feature Gate** | None |
| **Status** | New |

---

### F4. Bot Bookings Payment Pending

| Field | Detail |
| --- | --- |
| **Task Name** | Bot Booking Payments Pending |
| **Source Type** | System-detected |
| **Section** | Growth |
| **Meaning** | Online bookings through the booking bot with incomplete payment |
| **Operator Description** | "How many bot-generated bookings haven't completed payment?" These are online prospects who started booking but didn't pay. A quick follow-up can close them. |
| **Calculation Logic** | `COUNT(*) FROM booking_bot_users WHERE payment_status = 0 AND bed_reserved > 0` |
| **Source** | New — not yet implemented. Entity: `booking_bot_users` with `payment_status` field. |
| **Visibility** | Always shown when count > 0 |
| **Priority Color** | Orange |
| **Priority Tier** | T4 |
| **Sort Signal** | `count` |
| **Promotes To** | — |
| **Required Access** | `view_leads` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | Yes — midnight reset |
| **Title Pattern** | `{N} Bot Booking(s) Payment Pending` |
| **Subtitle** | Online bookings with incomplete payment — close them |
| **CTA** | `Follow Up` |
| **Destination** | Bot bookings list |
| **Icon** | `bot_booking.png` |
| **Urgency Text** | None |
| **Feature Gate** | Booking bot enabled |
| **Status** | New |

---

### F5. Tenants Not On App

| Field | Detail |
| --- | --- |
| **Task Name** | Tenants Not On App |
| **Source Type** | System-detected |
| **Section** | Growth |
| **Meaning** | Active tenants who haven't downloaded the tenant app — distinct from B9 in that this is positioned as a growth metric, not a people/onboarding task |
| **Operator Description** | "What percentage of your tenants are still off the app?" Every tenant not on the app means manual rent collection, manual complaints, manual everything. This is a business efficiency metric. |
| **Calculation Logic** | Same as B9: `COUNT(DISTINCT tenant) WHERE status = 1 AND onboarding_flag = false` |
| **Source** | `getAllTenants.ts:3037` (old system, filter_code: 102) — `numCode == 102` sets `onboarding_flag = false`; count at :1855 (`not_on_app` = `onboarding_flag = false AND status = 1`) and :2653 (`app_download_pending_count`) |
| **Visibility** | Always shown when count > 0. Note: B9 and F5 are the same underlying data — show in Growth if the Growth tab is the active view, in People if People is active. Do not show both simultaneously. |
| **Priority Color** | Blue |
| **Priority Tier** | T4 |
| **Sort Signal** | `count` |
| **Promotes To** | — |
| **Required Access** | `view_tenants` |
| **Access Logic** | ANY |
| **Aggregation** | `sum` |
| **Dismissible** | Yes — midnight reset |
| **Title Pattern** | `{N} Tenant(s) Not On App` |
| **Subtitle** | Every tenant off the app = more manual work for you |
| **CTA** | `Send Invite` |
| **Destination** | Tenant list filtered to no app installed |
| **Icon** | `app_adoption.png` |
| **Urgency Text** | None |
| **Feature Gate** | None |
| **Status** | New (cross-listed with B9) |

---

---

## G. PLATFORM

App health, platform subscription, communication balance, and system tasks.

---

### G1. RentOk Plan Expiring Soon

| Field | Detail |
| --- | --- |
| **Task Name** | Plan Expiring Soon |
| **Source Type** | Time-triggered |
| **Section** | Platform |
| **Meaning** | RentOk subscription plan expiring within 7 days |
| **Operator Description** | "Is your RentOk plan about to expire?" Renew before it lapses — feature access gets restricted after expiry. |
| **Calculation Logic** | `COUNT(*) FROM property_plan WHERE end_date BETWEEN CURRENT_DATE AND (CURRENT_DATE + INTERVAL '7 days')` |
| **Source** | New — not yet implemented. Entity: `property_plan` with `end_date`. Also: `rentokExpiringPlan.is_active` checked in `services/entryExit/entryExitService.ts:248-259`. |
| **Visibility** | Shown when plan expiry is within 7 days |
| **Priority Color** | Red |
| **Priority Tier** | T1 |
| **Sort Signal** | `deadline` |
| **Promotes To** | — |
| **Required Access** | `—` (universal) |
| **Access Logic** | ANY |
| **Aggregation** | `any` |
| **Dismissible** | No |
| **Title Pattern** | `Plan Expiring in {N} Days` |
| **Subtitle** | Renew to avoid service disruption |
| **CTA** | `Renew Now` |
| **Destination** | Billing/subscription screen |
| **Icon** | `plan_expiry.png` |
| **Urgency Text** | `Expires in {N} days` |
| **Feature Gate** | None |
| **Status** | New |

---

### G2. WhatsApp Balance Low

| Field | Detail |
| --- | --- |
| **Task Name** | WhatsApp Balance Low |
| **Source Type** | System-detected |
| **Section** | Platform |
| **Meaning** | WhatsApp message credits are running low |
| **Operator Description** | "Are you running out of WhatsApp message credits?" When credits hit zero, rent reminders, complaint alerts, and move-in notifications stop going out. Recharge before you go silent. |
| **Calculation Logic** | WhatsApp message balance below threshold (configurable, e.g., < 50 messages) |
| **Source** | New — not yet implemented. Requires balance calculation across recharge and usage tables. |
| **Visibility** | Shown when balance < threshold |
| **Priority Color** | Orange |
| **Priority Tier** | T3 |
| **Sort Signal** | `count` |
| **Promotes To** | T1 when balance = 0 |
| **Required Access** | `view_tenants` |
| **Access Logic** | ANY |
| **Aggregation** | `any` |
| **Dismissible** | Yes — midnight reset |
| **Title Pattern** | `WhatsApp Balance Low` |
| **Subtitle** | Only {N} messages left — recharge to keep reminders flowing |
| **CTA** | `Recharge Now` |
| **Destination** | WhatsApp recharge screen |
| **Icon** | `whatsapp.png` |
| **Urgency Text** | `{N} messages remaining` |
| **Feature Gate** | WhatsApp enabled |
| **Status** | New |

---

### G3. Eqaro Insurance (Deprecated)

| Field | Detail |
| --- | --- |
| **Task Name** | Insurance Claims Pending |
| **Source Type** | System-detected |
| **Section** | Platform |
| **Meaning** | Pending insurance claims via Eqaro/Ikaro integration |
| **Operator Description** | "Do you have pending insurance claims?" Note: The Eqaro (Ikaro) integration has been deprecated. This task type is no longer active and will not surface for new properties. Existing claims from before deprecation may still appear in historical data. |
| **Calculation Logic** | N/A — integration deprecated |
| **Source** | Deprecated — Eqaro/Ikaro integration shut down. Entity references may still exist in codebase. |
| **Visibility** | Not shown — deprecated |
| **Priority Color** | N/A |
| **Priority Tier** | N/A |
| **Sort Signal** | N/A |
| **Promotes To** | N/A |
| **Required Access** | N/A |
| **Access Logic** | N/A |
| **Aggregation** | N/A |
| **Dismissible** | N/A |
| **Title Pattern** | N/A |
| **Subtitle** | N/A |
| **CTA** | N/A |
| **Destination** | N/A |
| **Icon** | N/A |
| **Urgency Text** | N/A |
| **Feature Gate** | N/A |
| **Status** | Deprecated — Eqaro/Ikaro integration shut down (2025). Kept in registry for historical reference. |

---

---

---

## Appendix: Complete Task Inventory

### By Category

| # | Task | Category | Source Type | Priority Tier | Priority Color | Required Access | Dismissible | Aggregation | Status |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| A1 | Rent & Bills Overdue | Money | System-detected | T1 | Red | `view_invoices` | No | sum | Live (old) |
| A2 | Payments Not Linked | Money | System-detected | T2 | Orange | `view_invoices, record_payment` | Yes (midnight) | sum | Live (new) |
| A3 | Token Payments to Collect | Money | System-detected | T1 | Red | `view_invoices, view_tenants` | No | sum | Live (old) |
| A4 | Final Dues to Collect | Money | System-detected | T1 | Red | `view_invoices` | No | sum | Live (old) |
| A5 | Deposits to Refund | Money | System-detected | T2 | Orange | `add_refund_access` | Yes (midnight) | sum | Live (old) |
| A6 | Settlement Failed | Money | System-detected | T1 | Red | `bank_access` | No | sum | New |
| A7 | AutoPay Debits Failed | Money | System-detected | T1 | Red | `view_invoices` | No | sum | New |
| A8 | Wallet Payout Failed | Money | System-detected | T1 | Red | `bank_access` | No | sum | New |
| A9 | RentOk Charges Due | Money | Time-triggered | T1 | Red | `—` | No | any | New |
| A10 | AutoPay Manual Review | Money | System-detected | T2 | Orange | `view_invoices, record_payment` | Yes (midnight) | sum | New |
| A11 | Invoice Generation Failed | Money | System-detected | T1 | Red | `view_invoices, add_invoices` | No | sum | New |
| A12 | Online Settlement Pending | Money | System-detected | T3 | Orange | `bank_access` | Yes (midnight) | sum | New |
| A13 | Staff Salary & Reimb. Due | Money | Time-triggered | T2 | Orange | `view_team` | Yes (midnight) | sum | New |
| B1 | Booking Requests to Approve | People | Event-driven | T1 | Red | `view_tenants, add_tenants` | No | sum | Live (both) |
| B2 | Move-Out Requests | People | Event-driven | T1 | Red | `edit_eviction_access` | No | sum | Live (both) |
| B3 | Eviction Extensions to Review | People | Event-driven | T2 | Orange | `edit_eviction_access` | No | sum | Live (old) |
| B4 | Rate Departing Tenants | People | System-detected | T4 | Blue | `view_tenants` | Yes (midnight) | sum | Live (old) |
| B5 | Keys to Collect | People | System-detected | T2 | Orange | `key_handover_access` | Yes (midnight) | sum | Live (old) |
| B6 | Move-Ins This Week | People | Time-triggered | T3 | Green | `view_tenants` | Yes (midnight) | sum | Live (old) |
| B7 | Bookings Without Room | People | System-detected | T2 | Orange | `view_tenants, view_room` | Yes (midnight) | sum | Live (old) |
| B8 | Stale Bookings | People | System-detected | T3 | Orange | `view_tenants` | Yes (midnight) | sum | New |
| B9 | Tenants to Install App | People | System-detected | T4 | Blue | `view_tenants` | Yes (midnight) | sum | Live (old) |
| B10 | Eviction Requests Pending | People | Event-driven | T2 | Red | `edit_eviction_access` | No | sum | New |
| B11 | Guest Visit Requests | People | Event-driven | T2 | Orange | `view_tenants` | Yes (midnight) | sum | New |
| B12 | Move-Outs This Month | People | Time-triggered | T3 | Green | `view_tenants` | Yes (midnight) | sum | New |
| B13 | Contact Info Missing | People | System-detected | T4 | Orange | `view_tenants, edit_tenants` | Yes (midnight) | sum | New |
| C1 | Agreement Renewals Due | Compliance | Time-triggered | T3 | Orange | `view_tenants` | Yes (midnight) | sum | Live (both) |
| C2 | Renewed Agreement Unsigned | Compliance | System-detected | T2 | Red | `view_tenants` | Yes (midnight) | sum | Live (old) |
| C3 | Tenants Without Agreement | Compliance | System-detected | T2 | Red | `view_tenants` | Yes (midnight) | sum | Live (old) |
| C4 | Bookings Without Agreement | Compliance | System-detected | T3 | Orange | `view_tenants` | Yes (midnight) | sum | Live (old) |
| C5 | Tenants KYC Pending | Compliance | System-detected | T3 | Orange | `view_tenants` | Yes (midnight) | sum | Live (old) |
| C6 | Bookings KYC Pending | Compliance | System-detected | T3 | Orange | `view_tenants` | Yes (midnight) | sum | Live (old) |
| C7 | Police Verifications | Compliance | System-detected | T3 | Orange | `view_tenants` | Yes (midnight) | sum | Live (old) |
| C8 | KYC Credits Low | Compliance | System-detected | T3 | Orange | `view_tenants` | Yes (midnight) | any | New |
| C9 | E-Sign Pending | Compliance | System-detected | T3 | Orange | `view_tenants` | Yes (midnight) | sum | New |
| D1 | Complaints Unassigned | Property | Event-driven | T2 | Red | `view_complaint` | No | sum | Live (new) |
| D2 | Complaints Overdue | Property | System-detected | T1 | Red | `view_complaint, edit_complaint` | No | sum | New |
| D3 | Inspections Pending | Property | Time-triggered | T3 | Orange | `view_room` | Yes (midnight) | sum | Live (new) |
| D4 | Inspections Expired | Property | System-detected | T3 | Orange | `view_room` | Yes (midnight) | sum | New |
| D5 | Move-In Checklist Pending | Property | Event-driven | T2 | Orange | `submit_or_update_moveout_checklist_access` | Yes (midnight) | sum | Live (both) |
| D6 | Move-Out Checklist Not Locked | Property | Event-driven | T2 | Orange | `approve_moveout_checklist_access` | Yes (midnight) | sum | Live (old) |
| D7 | Meter Readings Due | Property | Time-triggered | T3 | Orange | `edit_electricity_meter_status` | Yes (midnight) | sum | New |
| D8 | Escalated Complaints L2 | Property | System-detected | T3 | Orange | `view_complaint, edit_complaint` | No | sum | New |
| D9 | Escalated Complaints L3 | Property | System-detected | T1 | Red | `view_complaint, edit_complaint` | No | sum | New |
| D10 | Overbooked Rooms | Property | System-detected | T3 | Orange | `view_room` | No | sum | New |
| D11 | Asset Warranty Expiring | Property | System-detected | T4 | Orange | `view_room` | Yes (midnight) | sum | New |
| E1 | Update Manager App | Daily Ops | System-detected | T5 | Blue | `—` | No | any | Live (new) |
| E2 | Review Food Menu | Daily Ops | Time-triggered | T5 | Blue | `view_food` | Yes (weekly Mon) | any | Live (new) |
| E3 | Review Expenses | Daily Ops | Time-triggered | T5 | Blue | `view_expenses` | Yes (weekly Sat) | any | Live (new) |
| E4 | Watch Latest Tutorial | Daily Ops | Time-triggered | T5 | Blue | `—` | Yes (permanent) | any | Live (new) |
| E5 | Exit Requests Pending | Daily Ops | Event-driven | T1 | Red | `view_tenants` | No | sum | New |
| E6 | Late Entry Requests | Daily Ops | Event-driven | T2 | Orange | `view_tenants` | No | sum | New |
| E7 | Late Check-In Requests | Daily Ops | Event-driven | T2 | Orange | `view_tenants` | No | sum | New |
| E8 | Leave Requests Pending | Daily Ops | Event-driven | T2 | Orange | `view_tenants` | No | sum | New |
| E9 | Attendance Not Marked | Daily Ops | Time-triggered | T3 | Orange | `view_tenants` | Yes (midnight) | any | New |
| E10 | Add-On Bookings to Confirm | Daily Ops | Event-driven | T2 | Orange | `view_tenants` | Yes (midnight) | sum | New |
| E11 | Team Salary Due | Daily Ops | Time-triggered | T2 | Orange | `view_team` | Yes (midnight) | sum | New |
| F1 | AutoPay Setup | Growth | System-detected | T4 | Orange | `view_tenants` | Yes (midnight) | sum | Live (old) |
| F2 | Visits Scheduled Today | Growth | Time-triggered | T3 | Green | `view_leads` | Yes (midnight) | sum | New |
| F3 | Leads Without Follow-Up | Growth | System-detected | T4 | Orange | `view_leads` | Yes (midnight) | sum | New |
| F4 | Bot Bookings Payment Pending | Growth | System-detected | T4 | Orange | `view_leads` | Yes (midnight) | sum | New |
| F5 | Tenants Not On App | Growth | System-detected | T4 | Blue | `view_tenants` | Yes (midnight) | sum | New |
| G1 | Plan Expiring Soon | Platform | Time-triggered | T1 | Red | `—` | No | any | New |
| G2 | WhatsApp Balance Low | Platform | System-detected | T3 | Orange | `view_tenants` | Yes (midnight) | any | New |
| G3 | Eqaro Insurance | Platform | System-detected | N/A | N/A | N/A | N/A | N/A | Deprecated |

**Total: 64 active tasks + 1 deprecated = 65 registry entries** (11 live in new system + 16 live in old system only + 36 new + 1 deprecated + 1 cross-listed B9/F5)

Note: B9 (Tenants to Install App) and F5 (Tenants Not On App) share the same underlying data — show only one at a time based on the active category tab.

---

### By Priority Tier

| Tier | Count | Tasks |
| --- | --- | --- |
| **T1** — Revenue at Risk / SLA Breach | 14 | A1, A3, A4, A6, A7, A8, A9, A11, B1, B2, D2, D9, E5, G1 |
| **T2** — Needs Human Decision | 19 | A2, A5, A10, A13, B3, B5, B7, B10, B11, C2, C3, D1, D5, D6, E6, E7, E8, E10, E11 |
| **T3** — Upcoming Deadline | 19 | A12, B6, B8, B12, C1, C4, C5, C6, C7, C8, C9, D3, D4, D7, D8, D10, E9, F2, G2 |
| **T4** — Data Quality / Growth | 8 | B4, B9, B13, D11, F1, F3, F4, F5 |
| **T5** — Informational / Periodic | 4 | E1, E2, E3, E4 |

---

### By Required Access

| Permission | Tasks That Require It |
| --- | --- |
| `—` (universal) | A9, E1, E4, G1 |
| `view_invoices` | A1, A2, A3, A4, A7, A10, A11 |
| `view_tenants` | A3, B1, B4, B6, B7, B8, B9, B11, B12, B13, C1–C9, E5–E9, F1, F5, G2 |
| `view_complaint` | D1, D2, D8, D9 |
| `view_room` | D3, D4, D10, D11 |
| `view_leads` | F2, F3, F4 |
| `view_food` | E2 |
| `view_expenses` | E3 |
| `view_team` | A13, E11 |
| `bank_access` | A6, A8, A12 |
| `record_payment` | A2, A10 |
| `add_refund_access` | A5 |
| `edit_eviction_access` | B2, B3, B10 |
| `key_handover_access` | B5 |
| `edit_electricity_meter_status` | D7 |
| `submit_or_update_moveout_checklist_access` | D5 |
| `approve_moveout_checklist_access` | D6 |
| `add_tenants` | B1 |
| `edit_tenants` | B13 |
| `edit_complaint` | D2, D8, D9 |
| `add_invoices` | A11 |

---

### Conditional Tasks (Feature-Gated)

| Task | Required Feature | Property Flag |
| --- | --- | --- |
| A7. AutoPay Debits Failed | AutoPay | autopay enabled |
| A8. Wallet Payout Failed | FlexiPe/Wallet | wallet enabled |
| A10. AutoPay Manual Review | AutoPay | autopay enabled |
| B5. Keys to Collect | Complete Handover | `complete_handover_enabled = 1` |
| B10. Eviction Requests Pending | New Eviction Flow | `is_new_eviction_flow = true` |
| B11. Guest Visit Requests | Guest Visit Feature | `tenant_app_host_friends` enabled |
| C8. KYC Credits Running Low | KYC Verification | KYC enabled for property |
| C9. E-Sign Agreement Pending | E-Sign | E-sign feature enabled |
| D5. Move-In Checklist Pending | Move-in/Move-out Checklist | `movein_moveout_checklist_enabled = 1` |
| D6. Move-Out Checklist Not Locked | Move-in/Move-out Checklist | `movein_moveout_checklist_enabled = 1` |
| D7. Meter Readings Due | Electricity/Meter Billing | meter billing enabled |
| D11. Asset Warranty Expiring | Asset/Inventory | asset management enabled |
| E2. Review Food Menu | Food Management | `food_attendance_enabled = 1` |
| E5. Exit Requests Pending | Entry/Exit System | entry/exit enabled |
| E6. Late Entry Requests | Entry/Exit System | entry/exit enabled |
| E7. Late Check-In Requests | Attendance | `is_attendance_enabled = 1` |
| E8. Leave Requests Pending | Attendance | `is_attendance_enabled = 1` |
| E9. Attendance Not Marked | Attendance Tracking | `is_attendance_enabled = 1` |
| E10. Add-On Bookings | Add-On Services | add-on services enabled |
| F1. AutoPay Setup | AutoPay | autopay enabled |
| F4. Bot Bookings | Booking Bot | booking bot enabled |
| G2. WhatsApp Balance Low | WhatsApp | WhatsApp enabled |

---

### Implementation Phases

**Phase 1 — Migrate existing tasks (0 new queries, just wire existing logic)**
16 tasks from old quick_filter system that need migration to new homepage: A1, A3, A4, A5, B3, B4, B5, B6, B7, B9, C2, C3, C4, C5, C6, C7, D6, F1.

**Phase 2 — Quick wins (simple COUNT queries on existing entities)**
15 new tasks with low complexity: A6, A7, A8, A10, A11, B8, B10, B11, D2, D4, D8, D9, D10, E5, E6.

**Phase 3 — Medium lift (cross-table joins, balance calculations, or threshold logic)**
12 new tasks with medium complexity: A9, A12, A13, B12, B13, C8, C9, D7, D11, E7, E8, E9.

**Phase 4 — Growth & platform (new entity setup or external integration)**
6 new tasks: E10, E11, F2, F3, F4, G2.

**Infrastructure prerequisites:**
- Role-based task filtering (extend `VisibilityContext` to check `Required Access` per task)
- Priority sort (add `compareTasks()` sort before returning the task array)
- Assignee resolution (query `team_member_property` for matching permissions)
- Multi-property aggregation labels ("from N properties")
- Task configuration table (move from hardcoded to DB-driven registry)

---

### Structural Gaps: PRD vs. Code

These architectural changes are prerequisites before scaling to the full registry. Verified against `master` on 2026-07-21 — the live feed is `getPendingTasks()` in `src/v1/homepage/service.ts` (~line 1310), route `GET /v1/home/analytics/pending-tasks`.

| Gap | What the target spec wants | What the code does today | Severity |
| --- | --- | --- | --- |
| No task config table | "Product/ops can add, remove, rename, reorder without an app release" | The ~10 live task types are hardcoded inline (`tasks.push({...})`). Adding one = code change + deploy. | Critical |
| No priority/ranking | "Backend-ranked shortlist (top N)" | Tasks are emitted in code order. No scoring, no dynamic tier. See Priority Framework section above for the full spec. | High |
| No assignee visibility | "Admin sees who is responsible for each task" | No assignee info on any task card. See Assignee Visibility section above for the full spec. | High |
| No multi-property attribution | "When viewing multiple properties, show which properties contribute" | Counts are aggregated but no "from N properties" label. See Multi-Property Aggregation section above. | Medium |
| No top-N truncation | "Homescreen shows top N, not the full task universe. N is backend-configurable." | All tasks with count > 0 and not dismissed are shown. No limit. | High |
| No per-task icon | "Icon: required" per card schema | The new `tasks[]` has no icon field (only a per-category icon array exists). | Medium |
| No urgency text | "Urgency text or due-state copy: optional" per card schema | Field doesn't exist in the card object. No task emits urgency text. | Medium |
| No View All search | "Search input at the top" of View All | Same endpoint serves both surfaces. No search parameter in schema. | Medium |

> [!NOTE] Role filtering is NOT a gap — it shipped.
> An earlier version of this table listed "no role filtering" as Critical. That is now false. Per-property permission gating is live: `resolveVisibilityContext` resolves owner-vs-team-member and per-property permissions from `TeamMemberProperty`, and every task is gated on `can_view_*`. The feed gates on ~6 of the ~90 permission flags today; extending to finer-grained flags is future work, but the mechanism exists.
