# Property

These cards appear in View All under Property. Home may show at most one of them at a time in the stack.

Complaints, inspections, move-in and move-out checklists, meters, rooms, asset warranties.

**This is not the checklist / task / schedule Task module.**

This follows the sourced registry. Live-vs-code is in [GAPS.md](GAPS.md).

These cards are count only. No ₹ on the title. Compact ₹ only when How we count names an amount. None of these cards name one. Ruled [NEED-YOU #5](NEED-YOU.md) A, 16 Aug 2026.

---

## D1. Complaints unassigned (Property)

**Registry status:** Live on the new homepage.

**The situation:** A tenant raised a complaint. Nobody is on it. Until you assign someone, it sits. They follow up or it jumps a level.

**What the card shows:** **{N} Complaint(s) Unassigned**. N is how many complaints with nobody assigned. Subtitle: Assign before tenants follow up. Colour: red. Urgency: none. Button: Assign Now. Tap: the complaints list, already filtered to unassigned. Cannot snooze. Hide if 0. After any of them has sat more than 24 hours with nobody on it, it jumps up the list. Someone who can only view complaints still sees Assign Now.

**Who sees it:** Anyone who can view complaints on at least one property you have selected. Owner and admin always.

**How we count:** N is complaints with nobody assigned, not marked resolved. Several properties selected: add the numbers together. Past the expected-fix date is [complaints overdue](#d2-complaints-overdue-property). Open in the L2 window is [escalated L2](#d8-escalated-complaints-l2-property). Past L3 is [escalated L3](#d9-escalated-complaints-l3-property). Four jobs. Do not merge.

**Missing:** A leftover suggestion would also drop another closed status. The formula does not. Those rows still sit on this card.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Complaints Unassigned
- **Source:** `service.ts:1460` (query), rendered as task `unassigned_complaints` at `service.ts:1554–1563` (new system, block-based v2 feed). Source type: event-driven.
- **Access:** `view_complaint`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(*) FROM complaints WHERE property_id = ANY(...) AND team_member_id IS NULL AND status != 5 (resolved)`. Live code at `service.ts:1463` only excludes status 5. Escalation code (`escalateComplaint.ts`) also uses only `status != 5` / `Not(5)`. Adding `AND status != 10` remains a consistency suggestion only.
- **Tap/filter:** Complaints list filtered to unassigned. No filter_code in the registry.
- **Icon:** `complaint_unassigned.png`.
- **Sort/promotion:** T2, sort by `count`. Promotes To: T1 when any unassigned complaint > 24 hours old.
- **Dismissible:** No.
- **Assignee Source:** `ComplaintResponderMap` — complaint category → designated responder mapping, not just anyone with `view_complaint`. See [RULES](RULES.md#assignee-chip).
- **Meaning as written:** Complaints raised by tenants with no staff member assigned.
- **Status leftover:** Live (new system).

</details>

---

## D2. Complaints overdue (Property)

**Registry status:** New.

**The situation:** A complaint has passed the date it was supposed to be fixed. They are waiting. Escalate it or close it.

**What the card shows:** **{N} Complaint(s) Overdue**. N is how many complaints past that date. Subtitle: Past resolution date — escalate or resolve now. Colour: red. Urgency: Past deadline. Button: Resolve Now. Tap: the complaints list, already filtered to overdue. Cannot snooze. Hide if 0. Someone who can only view complaints still sees Resolve Now.

**Who sees it:** Anyone who can view complaints or edit complaints on at least one property you have selected. Owner and admin always.

**How we count:** N is complaints not resolved, with an expected-fix date, and that date is already past. Several properties selected: add the numbers together. Nobody assigned is [complaints unassigned](#d1-complaints-unassigned-property). Open in the L2 window is [escalated L2](#d8-escalated-complaints-l2-property). Past L3 is [escalated L3](#d9-escalated-complaints-l3-property). This card is the expected-fix date, not how long it has been open. Four jobs. Do not merge.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Complaints Overdue
- **Source:** New — not yet implemented. Column `expected_resolution_date` exists on complaints entity. Source type: system-detected.
- **Access:** `view_complaint, edit_complaint`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(*) FROM complaints WHERE status NOT IN (5, 10) AND expected_resolution_date IS NOT NULL AND expected_resolution_date < CURRENT_DATE`.
- **Tap/filter:** Complaints list filtered to overdue. No filter_code in the registry.
- **Icon:** `complaint_overdue.png`.
- **Sort/promotion:** T1, sort by `days_overdue`. Promotes To: —.
- **Dismissible:** No.
- **Assignee Source:** Complaint's assigned `team_member_id` — the person already working on it. See [RULES](RULES.md#assignee-chip).
- **Meaning as written:** Complaints that have passed their expected resolution date.
- **Status leftover:** New.

</details>

---

## D3. Inspections pending (Property)

**Registry status:** The new homepage copy of this card is switched off. The count is still fetched. It is not shown. Not on the old home screen.

**The situation:** Inspections were due today. The team has not submitted results. Mark them done or move them. Do not let them pile up.

**What the card shows:** **{N} Inspection(s) Pending**. N is how many inspections still pending today. Subtitle: Mark complete or reschedule. Colour: orange. Urgency: Today. Button: View Inspections. Tap: the inspection list, already filtered to pending. Can snooze. Comes back at midnight. Hide if 0. Snoozing does not mark an inspection. Someone who can only view rooms still sees View Inspections.

**Who sees it:** Anyone who can view rooms on at least one property you have selected. Owner and admin always.

**How we count:** N is inspection rows still pending whose created date is today. Several properties selected: add the numbers together. Missed entirely is [inspections expired](#d4-inspections-expired-property). Two jobs. Do not merge.

**Missing:** The meaning says scheduled for today. The formula is created today.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Inspections Pending
- **Source:** Query at `service.ts:1470–1478` (computes `inspectionsRows`), but the task-render block is COMMENTED OUT / dead at `service.ts:1625–1634` — the count is fetched yet never surfaced. NOT live. Source type: time-triggered.
- **Access:** `view_room`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(*) FROM task_instance JOIN task_schedule WHERE task_instance.status = 'pending' AND DATE(task_instance.created_at) = CURRENT_DATE`.
- **Tap/filter:** Inspection list filtered to pending. No filter_code in the registry.
- **Icon:** `inspection.png`.
- **Sort/promotion:** T3, sort by `count`. Promotes To: —.
- **Dismissible:** Yes — midnight reset.
- **Meaning as written:** Room/property inspections scheduled for today but not yet completed.
- **Status leftover:** Appendix Live (new). Card Status: NOT live — `inspection_pending` render block is commented-out dead code in `getPendingTasks()`.

</details>

---

## D4. Inspections expired (Property)

**Registry status:** New.

**The situation:** An inspection was scheduled. Nobody submitted it. It expired. Reschedule it or mark it. A pile of expired inspections means the process is broken.

**What the card shows:** **{N} Inspection(s) Expired**. N is how many inspections that expired with no submit. Subtitle: Missed inspections — reschedule or review. Colour: orange. Urgency: Missed. Button: Review Now. Tap: the inspection list, already filtered to expired. Can snooze. Comes back at midnight. Hide if 0. Snoozing does not reschedule anyone. Someone who can only view rooms still sees Review Now.

**Who sees it:** Anyone who can view rooms on at least one property you have selected. Owner and admin always.

**How we count:** N is inspection rows whose status is expired. Several properties selected: add the numbers together. Still pending today is [inspections pending](#d3-inspections-pending-property). Two jobs. Do not merge.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Inspections Expired
- **Source:** New — not yet implemented. Note: `task_instance` is a raw table (queried via raw SQL in `service.ts`), not a TypeORM entity — no `task_instance` entity file exists, so the `'expired'` status enum could not be verified from an entity definition. Source type: system-detected.
- **Access:** `view_room`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(*) FROM task_instance WHERE status = 'expired'`.
- **Tap/filter:** Inspection list filtered to expired. No filter_code in the registry.
- **Icon:** `inspection_expired.png`.
- **Sort/promotion:** T3, sort by `count`. Promotes To: —.
- **Dismissible:** Yes — midnight reset.
- **Meaning as written:** Inspections that were never completed and have expired.
- **Status leftover:** New.

</details>

---

## D5. Move-in checklist (room) (Property)

**Registry status:** Live on the old home screen and the new homepage. Old and new count them differently.

**The situation:** They moved in. Staff walked the room — fan, mattress, walls. It is waiting for you to lock. If there is a fight at move-out, this is your paper. Only if the move-in / move-out checklist is on.

**What the card shows:** **{N} Move-In Checklist(s) Pending**. N is how many submitted move-in room checklists not locked yet. Subtitle: Room and assets as they were on day one. Colour: orange. Urgency: none. Button: Review Now. Tap: the room checklist list waiting for lock (live filter 2384). Can snooze. Comes back at midnight. Hide if 0. Snoozing does not lock anyone. Someone who can submit or update a move-out checklist still sees Review Now.

**Who sees it:** Anyone who can submit or update a move-out checklist on at least one property you have selected. Owner and admin always. That is the move-out checklist permission, not a separate move-in one. Only if the move-in / move-out checklist is on. Spec and live disagree: spec is the move-out checklist permission; live new home is view tenants. Not a silent pick.

**How we count:** N is a submitted move-in room checklist, not locked. Not Aadhaar. New homepage: submitted, waiting to lock. Old home screen: also people with no checklist yet, or still in earlier statuses. Several properties selected: add only from properties where the checklist is on. Papers (Aadhaar) is [tenants to complete KYC](COMPLIANCE.md#c5-tenants-to-complete-kyc-compliance). This card is the room. A move-out checklist still open is [move-out checklist not locked](#d6-move-out-checklist-not-locked-property). Two jobs. Do not merge.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Move-In Checklist Pending; Move-In Verifications Pending. Title leftover: `{N} Move-In Verification(s) Pending`. Subtitle leftover: Verify tenant documents. CTA leftover: Verify Now. Destination leftover: KYC/document verification worklist. Do not retarget to filter 201 (that is C5).
- **Source:** New system: query at `service.ts:1404–1411`, rendered as task `move_in_checklist_pending` at `service.ts:1600` (category is `People` in code, not `Property`). Old system: `src/services/property/getAllTenants.ts` (moved from `src/controllers/`); line 2355 and filter_codes 5010/2384 not found on current master — unverified/stale. Source type: event-driven.
- **Access:** `submit_or_update_moveout_checklist_access`, ANY. Spec leftover. Live new home: `view_tenants`. Clash is loud on the card. Not a silent pick.
- **Aggregation:** `sum`.
- **Calc as written:** New system: `COUNT(*) FROM tenant_checklist WHERE checklist_type = 'move_in' AND status = 1`. Old system (broader): `checklist IS NULL OR status < 2 OR status = 3`. Product count: submitted move-in room checklist, not locked. Not Aadhaar.
- **Tap/filter:** Product tap is the room checklist list waiting for lock (live filter 2384). Spec leftover: KYC/document verification worklist. Do not retarget to filter 201. Old filter_codes 5010/2384 unverified on current master.
- **Icon:** `checklist.png`.
- **Sort/promotion:** T2, sort by `count`. Promotes To: —.
- **Dismissible:** Yes — midnight reset.
- **Feature gate:** `movein_moveout_checklist_enabled = 1`.
- **Product (NEED-YOU Property D5 A, 16 Aug 2026):** D5 is the room walkthrough. C5 is papers. Title is `{N} Move-In Checklist(s) Pending`. Button is Review Now. Verification, Verify Now, and spec tap KYC are registry leftovers only. Do not point D5 at KYC.
- **Meaning as written:** Tenants who have submitted their move-in checklist but it hasn't been verified by the manager.
- **Status leftover:** Live (both systems). Code category leftover: `People`.

</details>

---

## D6. Move-out checklist not locked (Property)

**Registry status:** Live on the old home screen. Not on the new homepage yet.

**The situation:** A move-out checklist is still open. Until you lock it, the damage call is not official. Lock it before you return deposit. Only if the move-in / move-out checklist is on.

**What the card shows:** **{N} Move-Out Checklist(s) Not Locked**. N is how many move-out checklists still open. Subtitle: Lock before processing deposit refund. Colour: orange. Urgency: none. Button: Lock Now. Tap: the move-out checklist worklist. Can snooze. Comes back at midnight. Hide if 0. Snoozing does not lock anyone.

**Who sees it:** Anyone who can approve a move-out checklist on at least one property you have selected. Owner and admin always. Only if the move-in / move-out checklist is on.

**How we count:** N is staying people at a property where the checklist is on, with a move-out checklist far enough along to lock and not locked yet. Several properties selected: add only from properties where the checklist is on. A submitted move-in room checklist not locked is [move-in checklist (room)](#d5-move-in-checklist-room-property). Two jobs. Do not merge. Returning leftover deposit is [deposits to refund](MONEY.md#a5-deposits-to-refund-money). This card is lock the checklist, not refund.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Move-Out Checklist Not Locked
- **Source:** `src/services/property/getAllTenants.ts` (moved from `src/controllers/`); line 2366 and filter_code 2385 not found on current master — unverified/stale, likely drifted. Source type: event-driven.
- **Access:** `approve_moveout_checklist_access`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(DISTINCT tenant) WHERE status = 1 AND movein_moveout_checklist_enabled = 1 AND checklist IS NOT NULL AND checklist.status >= 4 AND checklist.status < 6`.
- **Tap/filter:** Move-out checklist worklist. Old filter_code 2385 unverified.
- **Icon:** `checklist.png`.
- **Sort/promotion:** T2, sort by `count`. Promotes To: —.
- **Dismissible:** Yes — midnight reset.
- **Feature gate:** `movein_moveout_checklist_enabled = 1`.
- **Meaning as written:** Move-out checklists in progress but not finalized.
- **Status leftover:** Live (old system) — needs migration to new homepage.

</details>

---

## D7. Meter readings due (Property)

**Registry status:** New.

**The situation:** An active meter has no reading this billing cycle. Missing readings become estimated bills. People fight estimates. Record them before this cycle's bills. Only if meter billing is on.

**What the card shows:** **{N} Meter Reading(s) Due**. N is how many active meters with no reading this cycle. Subtitle: Record before generating this cycle's bills. Colour: orange. Urgency: This cycle. Button: Record Now. Tap: the meter readings screen. Can snooze. Comes back at midnight. Hide if 0. Snoozing does not record a reading.

**Who sees it:** Anyone who can edit electricity meter status on at least one property you have selected. Owner and admin always. Only if meter billing is on.

**How we count:** N is active meters with no reading for the current billing cycle. Several properties selected: add only from properties where meter billing is on.

**Missing:** "Typically the last week of a billing cycle" is not a hide formula. Hide if 0. What counts as this billing cycle is not named beyond "no reading for the current billing cycle".

<details>
<summary>For engineering</summary>

- **Registry also listed:** Meter Readings Due
- **Source:** New — not yet implemented. Entities: `meters`, `meter_historical_reading`. Source type: time-triggered.
- **Access:** `edit_electricity_meter_status`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(*) FROM meters WHERE is_active = 1 AND NOT EXISTS(meter_historical_reading for current billing cycle)`.
- **Tap/filter:** Meter readings screen. No filter_code in the registry.
- **Icon:** `meter.png`.
- **Sort/promotion:** T3, sort by `deadline`. Promotes To: —.
- **Dismissible:** Yes — midnight reset.
- **Feature gate:** Electricity/meter billing enabled.
- **Visibility leftover:** Shown when count > 0, typically in the last week of a billing cycle.
- **Meaning as written:** Active meters that don't have a reading recorded for the current billing cycle.
- **Status leftover:** New.

</details>

---

## D8. Escalated complaints L2 (Property)

**Registry status:** New.

**The situation:** A complaint has been open long enough to hit L2. Default: 48 hours, still under 72. The system already moved it. You still need someone working it. This card is L2. Past 72 hours is the L3 card.

**What the card shows:** **{N} Complaint(s) Escalated**. N is how many open complaints in the L2 window. Subtitle: 48+ hours unresolved — intervention needed. Colour: orange. Urgency: 48+ hours. Button: Resolve Now. Tap: the complaints list, already filtered to escalated L2. Cannot snooze. Hide if 0. When any of them is close to the L3 time (72 hours if none is set) it jumps up the list. Someone who can only view complaints still sees Resolve Now.

**Who sees it:** Anyone who can view complaints or edit complaints on at least one property you have selected. Owner and admin always.

**How we count:** N is open complaints whose age sits between the L2 time and the L3 time. Default: open 48 to 72 hours. Times can be set per property per complaint type. Several properties selected: add the numbers together. Nobody assigned is [complaints unassigned](#d1-complaints-unassigned-property). Past the expected-fix date is [complaints overdue](#d2-complaints-overdue-property). Past L3 is [escalated L3](#d9-escalated-complaints-l3-property). Four jobs. Do not merge. Subtitle says 48+. The count is the L2 window, not everything 48 hours and older.

**Missing:** How close to L3 counts as "approaching" for the jump.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Escalated Complaints — L2; Complaints Escalated (L2)
- **Source:** New — L2 escalation logic in `src/services/complaints/escalateComplaint.ts:113–121` (`Between` window at line 117); a newer parallel implementation exists in `escalate_v2.ts` (uses `effective_l2_tat` + `isAfter`, no `Between` window). Not surfaced as a pending task. Source type: system-detected.
- **Access:** `view_complaint, edit_complaint`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(*) FROM complaints WHERE status NOT IN (5, 10) AND created_at BETWEEN (NOW() - l3_time hours) AND (NOW() - l2_time hours)`. Default: complaints open 48–72 hours. Escalation times are configurable per property per complaint category via `ComplaintEscalationTime` (defaults: L1=24h, L2=48h, L3=72h). The escalation cron uses a narrow 1-hour window (`Between(now - (l2+1)h, now - l2h)`) to trigger notifications — the pending task should use the full L2 range (l2 to l3) for counting.
- **Tap/filter:** Complaints list filtered to escalated L2. No filter_code in the registry.
- **Icon:** `complaint_escalated.png`.
- **Sort/promotion:** T3, sort by `days_overdue`. Promotes To: T2 when any L2 complaint approaching L3 threshold (72h).
- **Dismissible:** No.
- **Assignee Source:** Escalation responder from `ComplaintResponderMap` — the L2 assignee. See [RULES](RULES.md#assignee-chip).
- **Counted leftover:** `{N} Complaint(s) Escalated` is the product title. L2 is the heading and the filter, not a second title.
- **Meaning as written:** Complaints unresolved for 48+ hours that have been auto-escalated to L2.
- **Status leftover:** New.

</details>

---

## D9. Escalated complaints L3 (Property)

**Registry status:** New.

**The situation:** A complaint has been open long enough to hit L3. Default: 72 hours or more. The levels have run out. This needs owner or admin attention. This card is L3. The 48-to-72 window is the L2 card.

**What the card shows:** **{N} Complaint(s) Critical**. N is how many open complaints past the L3 time. Subtitle: 72+ hours unresolved — owner/admin attention required. Colour: red. Urgency: 72+ hours — SLA breach. Button: Resolve Now. Tap: the complaints list, already filtered to L3 escalated. Cannot snooze. Hide if 0. Someone who can only view complaints still sees Resolve Now.

**Who sees it:** Anyone who can view complaints or edit complaints on at least one property you have selected. Owner and admin always.

**How we count:** N is open complaints older than the L3 time. Default: open 72 hours or more. Includes ones the nightly job has stopped pinging. Several properties selected: add the numbers together. Nobody assigned is [complaints unassigned](#d1-complaints-unassigned-property). Past the expected-fix date is [complaints overdue](#d2-complaints-overdue-property). In the L2 window is [escalated L2](#d8-escalated-complaints-l2-property). Four jobs. Do not merge.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Escalated Complaints — L3; Complaints Critical (L3)
- **Source:** New — L3 escalation logic in `src/services/complaints/escalateComplaint.ts:137–145` (`Between` window at line 140); newer parallel implementation in `escalate_v2.ts` (uses `effective_l3_tat`). Not surfaced as a pending task. Source type: system-detected.
- **Access:** `view_complaint, edit_complaint`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(*) FROM complaints WHERE status NOT IN (5, 10) AND created_at < (NOW() - l3_time hours)`. Default: complaints open 72+ hours. Configurable per property per complaint category via `ComplaintEscalationTime` (default L3=72h). The escalation cron uses a 100-hour window (`Between(now - (l3+100)h, now - l3h)`) to catch complaints up to 172h old — complaints older than that are no longer actively escalated by the cron but should still appear in the pending task count.
- **Tap/filter:** Complaints list filtered to L3 escalated. No filter_code in the registry.
- **Icon:** `complaint_critical.png`.
- **Sort/promotion:** T1, sort by `days_overdue`. Promotes To: — (already T1).
- **Dismissible:** No.
- **Assignee Source:** Escalation authority from `ComplaintResponderMap` — the L3 assignee. See [RULES](RULES.md#assignee-chip).
- **Urgency leftover:** `72+ hours — SLA breach` is the parent urgency string.
- **Meaning as written:** Complaints unresolved for 72+ hours that have been auto-escalated to L3 (highest level).
- **Status leftover:** New.

</details>

---

## D10. Overbooked rooms (Property)

**Registry status:** New.

**The situation:** A room has more people assigned than beds. That is a data error or a real double-booking. Fix it.

**What the card shows:** **{N} Room(s) Overbooked**. N is how many rooms over capacity. Subtitle: More tenants than beds — reassign or add capacity. Colour: orange. Urgency: Over capacity. Button: Fix Now. Tap: the room list, already filtered to overbooked. Cannot snooze. Hide if 0. When an overbooked room has someone moving in within 7 days, it jumps up the list. Someone who can only view rooms still sees Fix Now.

**Who sees it:** Anyone who can view rooms on at least one property you have selected. Owner and admin always.

**How we count:** N is rooms where people assigned exceed the sharing capacity. Several properties selected: add the numbers together.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Overbooked Rooms; Rooms Overbooked
- **Source:** `controllers/rooms.ts:1724` (detection — `sharing_type < roomActiveTenantCount` sets `is_overbook_rooms`), `v1/constants/filterCodes.ts:151-152` (OVERBOOKED=1413, OVERBOOKED_BEDS=1414). Source type: system-detected.
- **Access:** `view_room`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(*) FROM rooms WHERE active_tenant_count > sharing_type` — sharing_type defines room capacity.
- **Tap/filter:** Room list filtered to overbooked (filter_code: 1413). OVERBOOKED_BEDS=1414 is a leftover pointer, not a second card.
- **Icon:** `overbooked.png`.
- **Sort/promotion:** T3, sort by `count`. Promotes To: T2 when an overbooked room has a new tenant move-in within 7 days.
- **Dismissible:** No.
- **Meaning as written:** Rooms where active tenant count exceeds the room's sharing capacity.
- **Status leftover:** New.

</details>

---

## D11. Asset warranty expiring (Property)

**Registry status:** New.

**The situation:** An asset's warranty ends within 30 days. File a claim or arrange repair before it runs out. After that you pay. Only if asset management is on.

**What the card shows:** **{N} Asset Warranty(s) Expiring**. N is how many assets in that window. Subtitle: File claims before warranty expires. Colour: orange. Urgency: Within 30 days. Button: Review Assets. Tap: the asset list, already filtered to expiring warranty. Can snooze. Comes back at midnight. Hide if 0. Snoozing does not file a claim. Someone who can only view rooms still sees Review Assets.

**Who sees it:** Anyone who can view rooms on at least one property you have selected. Owner and admin always. Only if asset management is on.

**How we count:** N is active assets whose warranty end date is today through 30 days from now. Several properties selected: add only from properties where asset management is on.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Asset Warranty Expiring; Asset Warranties Expiring
- **Source:** New — entity: `inventory.ts` with `AssetStatus` and `WarrantyStatus` enums including `EXPIRING_SOON`. Source type: system-detected.
- **Access:** `view_room`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** Columns must be computed, not read: `inventory` has NO `warranty_status` column and NO `warranty_end_date`. Actual columns are `warranty_expiry_date` (date) and a single `status` column holding `AssetStatus` (not `WarrantyStatus`). `WarrantyStatus.EXPIRING_SOON` is an enum only — never persisted. Correct logic: `COUNT(*) FROM inventory WHERE is_active = true AND warranty_expiry_date BETWEEN CURRENT_DATE AND (CURRENT_DATE + INTERVAL '30 days')`.
- **Tap/filter:** Asset list filtered to expiring warranty. No filter_code in the registry.
- **Icon:** `warranty.png`.
- **Sort/promotion:** T4, sort by `deadline`. Promotes To: —.
- **Dismissible:** Yes — midnight reset.
- **Feature gate:** Asset/inventory management enabled.
- **Meaning as written:** Inventoried assets with warranty expiring within 30 days.
- **Status leftover:** New.

</details>
