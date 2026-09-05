# Daily Ops

These cards appear in View All under Daily Ops. Home may show at most one of them at a time in the stack.

App update, food menu, expenses, tutorials, gate and attendance requests, add-on bookings, salary reminder.

**This is not the checklist / task / schedule Task module.**

This follows the sourced registry. Live-vs-code is in [GAPS.md](GAPS.md).

Counted title when N exists. Presence only when there is no N. No ₹ on any of these cards. Compact ₹ only when How we count names an amount. None of these cards name one. Ruled [NEED-YOU #5](NEED-YOU.md) A, 16 Aug 2026.

E5, E6, E7, and E8 are four different request types. Do not merge. E11 is a salary reminder. Reimbursement is [A13](MONEY.md#a13-reimbursement-due-money). Ruled [NEED-YOU #3](NEED-YOU.md) A+C, 16 Aug 2026.

---

## E1. Update manager app (Daily Ops)

**Registry status:** Live on the new homepage.

**The situation:** This phone is on an old Manager app. Update it so you have the latest fixes.

**What the card shows:** **New Update Available**. Presence only. No count and no ₹ on the title. Subtitle: Get the latest features and fixes. Colour: blue. Urgency: none. Button: Update Now. Tap: the app store / in-app update flow. Cannot snooze. Hide when this phone is already on the latest build.

**Who sees it:** Everyone with app access.

**How we count:** Show if this phone's build is behind the latest. The number on the card is 1, not how many properties. Android and iOS are compared separately. Several properties selected: still one card. Show if any match.

<details>
<summary>For engineering</summary>

- **Registry also listed:** New Update Available
- **Source:** `service.ts:1565` (new system). Source type: system-detected.
- **Access:** `—` (universal), ANY.
- **Aggregation:** `any`.
- **Calc as written:** `internal_config` key='latest_build_number', compare with client's `x-build-number` header. Platform-specific (Android vs iOS).
- **Visibility as written:** Shown when client build < latest build.
- **Tap/filter:** App store / in-app update flow. No filter_code in the registry.
- **Icon:** `app_update.png`.
- **Sort/promotion:** T5, sort by `static`. Promotes To: —.
- **Dismissible:** No.
- **Meaning as written:** Current app version is behind the latest release.
- **Status leftover:** Live (new system).

</details>

---

## E2. Review food menu (Daily Ops)

**Registry status:** Live on the new homepage.

**The situation:** It is Monday. People look at this week's menu to decide meals. If last week's menu is still up, they get the wrong food. Only if food is on.

**What the card shows:** **Update Food Menu**. Presence only. No count and no ₹ on the title. Subtitle: It's been a week — review your menu and timings. Colour: blue. Urgency: none. Button: Update Menu. Tap: the food menu screen. Can snooze. Comes back next Monday. Mondays only. Hide if they already updated the menu from this card this week.

**Who sees it:** Anyone who can view food on at least one property you have selected. Owner and admin always. Only if food is on.

**How we count:** Show on Monday if any selected property has food on. The number on the card is 1, not how many menus. Several properties selected: still one card. Add only from properties where food is on. Do not resurface if they already updated from this card this week.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Update Food Menu
- **Source:** `service.ts:1637` (new system). Source type: time-triggered.
- **Access:** `view_food`, ANY.
- **Aggregation:** `any`.
- **Calc as written:** Day-of-week = Monday AND property has `food_attendance_enabled = 1`. Don't resurface if user updated menu from this card in the current week.
- **Visibility as written:** Mondays only, food-enabled properties. Dismissible with weekly Monday reset.
- **Tap/filter:** Food menu management screen. No filter_code in the registry.
- **Icon:** `food_menu.png`.
- **Sort/promotion:** T5, sort by `static`. Promotes To: —.
- **Dismissible:** Yes — weekly Monday reset.
- **Feature gate:** `food_attendance_enabled = 1`.
- **Meaning as written:** Weekly reminder to review food menu (Monday only, food-enabled properties).
- **Status leftover:** Live (new system).

</details>

---

## E3. Review expenses (Daily Ops)

**Registry status:** Live on the new homepage.

**The situation:** It is Saturday. Small spends from the week get forgotten if you wait. Log them now.

**What the card shows:** **Pending Expenses**. Presence only. No count of unlogged expenses and no ₹ on the title. Subtitle: Week's ending — don't forget to log expenses. Colour: blue. Urgency: none. Button: Add Expenses. Tap: the expense entry screen. Can snooze. Comes back next Saturday. Saturdays only.

**Who sees it:** Anyone who can view expenses on at least one property you have selected. Owner and admin always.

**How we count:** Saturday reminder. Always count = 1. Not how many expenses are missing. No ₹. Several properties selected: still one card.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Pending Expenses
- **Source:** `service.ts:1649` (new system). Source type: time-triggered.
- **Access:** `view_expenses`, ANY.
- **Aggregation:** `any`.
- **Calc as written:** Day-of-week = Saturday. Always count = 1.
- **Visibility as written:** Saturdays only. Dismissible with weekly Saturday reset.
- **Tap/filter:** Expense entry screen. No filter_code in the registry.
- **Icon:** `expense.png`.
- **Sort/promotion:** T5, sort by `static`. Promotes To: —.
- **Dismissible:** Yes — weekly Saturday reset.
- **Meaning as written:** Saturday reminder to log the week's expenses.
- **Status leftover:** Live (new system).

</details>

---

## E4. Watch latest tutorial (Daily Ops)

**Registry status:** Live on the new homepage.

**The situation:** A new how-to video went up in the last 7 days. Watch it if you want the new workflow.

**What the card shows:** **New Tutorial Available**. Presence only. No count and no ₹ on the title. Subtitle: See what's new in the Manager App. Colour: blue. Urgency: none. Button: Watch Now. Tap: the tutorial / video screen. Can snooze. After the first view it stays gone. Hide after 7 days from publish, or after they dismiss.

**Who sees it:** Everyone with app access.

**How we count:** Show if any tutorial was published in the last 7 days. The number on the card is 1, not how many videos. Several properties selected: still one card.

<details>
<summary>For engineering</summary>

- **Registry also listed:** New Tutorial Available
- **Source:** `service.ts:1662` (new system). Source type: time-triggered.
- **Access:** `—` (universal), ANY.
- **Aggregation:** `any`.
- **Calc as written:** `internal_config` key='homepage_tutorials', filter videos where `published_at` is within last 7 days.
- **Visibility as written:** For 7 days after a new tutorial is published. Dismissible permanently (after first tap).
- **Tap/filter:** Tutorial / video screen. No filter_code in the registry.
- **Icon:** `tutorial.png`.
- **Sort/promotion:** T5, sort by `static`. Promotes To: —.
- **Dismissible:** Yes — permanent (after first view).
- **Meaning as written:** A new tutorial video was published within the last 7 days.
- **Status leftover:** Live (new system).

</details>

---

## E5. Exit requests pending (Daily Ops)

**Registry status:** New.

**The situation:** They want to go out. The gate is waiting on your yes. This is tonight at the door, not someone asking to leave for good. Only if entry-exit is on.

**What the card shows:** **{N} Exit Request(s) Pending**. N is how many exit requests still waiting. Subtitle: Tenants waiting at the gate — don't keep them waiting. Colour: red. Urgency: Waiting. Button: Approve Now. Tap: the entry/exit requests list, already filtered to exit. Cannot snooze. Hide if 0. Someone who can only view tenants still sees Approve Now.

**Who sees it:** Anyone who can view tenants on at least one property you have selected. Owner and admin always. Only if entry-exit is on.

**How we count:** N is pending EXIT requests. Several properties selected: add only from properties where entry-exit is on. After-hours entry at the same gate is [late entry](#e6-late-entry-requests-daily-ops). Late check-in on the attendance sheet is [late check-in](#e7-late-check-in-requests-daily-ops). Days away on leave is [leave requests](#e8-leave-requests-pending-daily-ops). Four jobs. Do not merge. Someone asking to leave the PG is [move-out requests](PEOPLE.md#b2-move-out-requests-people). This card is the gate.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Exit Requests Pending
- **Source:** Entity: `entryExitRequests.ts` with request_type and request_status fields. `entryExitService.ts:2071`. Source type: event-driven.
- **Access:** `view_tenants`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(*) FROM entry_exit_request WHERE request_type = 'EXIT' AND request_status = 'PENDING'`.
- **Tap/filter:** Entry/exit requests list filtered to exit. No filter_code in the registry.
- **Icon:** `entry_exit.png`.
- **Sort/promotion:** T1, sort by `count`. Promotes To: —.
- **Dismissible:** No.
- **Feature gate:** Entry/exit system enabled.
- **Meaning as written:** Tenants waiting for exit/leave approval via the entry-exit system.
- **Status leftover:** New.

</details>

---

## E6. Late entry requests (Daily Ops)

**Registry status:** New.

**The situation:** They came back after hours. The regular window is closed. They need a yes to come in. This is the gate, not the attendance late check-in. Only if entry-exit is on.

**What the card shows:** **{N} Late Entry Request(s)**. N is how many late-entry requests still waiting. Subtitle: Tenants requesting after-hours entry — review now. Colour: orange. Urgency: none. Button: Review Now. Tap: the entry/exit requests list, already filtered to late entry. Cannot snooze. Hide if 0. Someone who can only view tenants still sees Review Now.

**Who sees it:** Anyone who can view tenants on at least one property you have selected. Owner and admin always. Only if entry-exit is on.

**How we count:** N is pending LATE ENTRY requests. Several properties selected: add only from properties where entry-exit is on. Tonight's exit at the gate is [exit requests](#e5-exit-requests-pending-daily-ops). Late check-in on the attendance sheet is [late check-in](#e7-late-check-in-requests-daily-ops). Days away on leave is [leave requests](#e8-leave-requests-pending-daily-ops). Four jobs. Do not merge.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Late Entry Requests; Late Entry Requests Pending
- **Source:** Entity: `entryExitRequests.ts`. `entryExitService.ts:2055`. Source type: event-driven.
- **Access:** `view_tenants`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(*) FROM entry_exit_request WHERE request_type = 'LATE ENTRY' AND request_status = 'PENDING'`.
- **Tap/filter:** Entry/exit requests list filtered to late entry. No filter_code in the registry.
- **Icon:** `late_entry.png`.
- **Sort/promotion:** T2, sort by `count`. Promotes To: —.
- **Dismissible:** No.
- **Feature gate:** Entry/exit system enabled.
- **Meaning as written:** Tenants requesting late entry back into the property.
- **Status leftover:** New.

</details>

---

## E7. Late check-in requests (Daily Ops)

**Registry status:** New.

**The situation:** They missed check-in on the attendance sheet. Warden or parent still needs to say yes. This is not the after-hours gate card. Only if attendance is on.

**What the card shows:** **{N} Late Check-In Request(s)**. N is how many late check-in requests still waiting. Subtitle: Attendance requests awaiting approval. Colour: orange. Urgency: none. Button: Approve Now. Tap: the attendance pending-requests list. Cannot snooze. Hide if 0. Someone who can only view tenants still sees Approve Now.

**Who sees it:** Anyone who can view tenants on at least one property you have selected. Owner and admin always. Only if attendance is on.

**How we count:** N is late-checkin rows still waiting on warden or parent. Several properties selected: add only from properties where attendance is on. After-hours entry at the gate is [late entry](#e6-late-entry-requests-daily-ops). Tonight's exit at the gate is [exit requests](#e5-exit-requests-pending-daily-ops). Days away on leave is [leave requests](#e8-leave-requests-pending-daily-ops). Four jobs. Do not merge.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Late Check-In Requests; Late Check-In Requests Pending
- **Source:** Entity: parallel attendance system. `tenantAttendance.ts:782` for type check. Source type: event-driven.
- **Access:** `view_tenants`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(*) FROM tenant_attendance_pending_requests WHERE type = 'late-checkin' AND is_active = 1 AND (warden_approval = 0 OR parent_approval = 0)`. Approval states: `NULL`=not applicable (auto-approved), `0`=pending, `1`=approved, `-1`=rejected. DB column defaults to NULL; application explicitly sets `0` when that approval step is required.
- **Tap/filter:** Attendance pending requests list. No filter_code in the registry.
- **Icon:** `attendance_request.png`.
- **Sort/promotion:** T2, sort by `count`. Promotes To: —.
- **Dismissible:** No.
- **Feature gate:** `is_attendance_enabled = 1`.
- **Meaning as written:** Attendance-based late check-in requests awaiting manager/warden/parent approval.
- **Status leftover:** New.

</details>

---

## E8. Leave requests pending (Daily Ops)

**Registry status:** New.

**The situation:** They asked for leave. Attendance still needs a yes. This is days away, not tonight at the gate. Only if attendance is on.

**What the card shows:** **{N} Leave Request(s) Pending**. N is how many leave requests still waiting. Subtitle: Students waiting for leave approval. Colour: orange. Urgency: none. Button: Approve Now. Tap: the attendance pending-requests list, already filtered to leave. Cannot snooze. Hide if 0. Someone who can only view tenants still sees Approve Now.

**Who sees it:** Anyone who can view tenants on at least one property you have selected. Owner and admin always. Only if attendance is on.

**How we count:** N is leave rows still waiting on warden or parent. Several properties selected: add only from properties where attendance is on. Tonight's exit at the gate is [exit requests](#e5-exit-requests-pending-daily-ops). After-hours entry at the gate is [late entry](#e6-late-entry-requests-daily-ops). Late check-in on the attendance sheet is [late check-in](#e7-late-check-in-requests-daily-ops). Four jobs. Do not merge.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Leave Requests Pending
- **Source:** Entity: parallel attendance system. `tenantAttendance.ts:778` for type check. Source type: event-driven.
- **Access:** `view_tenants`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(*) FROM tenant_attendance_pending_requests WHERE type = 'leave' AND is_active = 1 AND (warden_approval = 0 OR parent_approval = 0)`. Approval states: `NULL`=not applicable, `0`=pending, `1`=approved, `-1`=rejected. Note: the `type` column stores lowercase values (`'leave'`, `'late-checkin'`), but the service also checks `'Leave'` at `tenantAttendance.ts:804` — query should be case-insensitive or match both.
- **Tap/filter:** Attendance pending requests list filtered to leave. No filter_code in the registry.
- **Icon:** `leave_request.png`.
- **Sort/promotion:** T2, sort by `count`. Promotes To: —.
- **Dismissible:** No.
- **Feature gate:** `is_attendance_enabled = 1`.
- **Meaning as written:** Attendance-based leave requests awaiting approval.
- **Status leftover:** New.

</details>

---

## E9. Attendance not marked today (Daily Ops)

**Registry status:** New.

**The situation:** Attendance is on. Nobody has marked today. A blank day is a hole in the record. Only if attendance is on.

**What the card shows:** **Attendance Not Marked Today**. Presence only. No count of unmarked people and no ₹ on the title. Subtitle: Mark today's attendance before end of day. Colour: orange. Urgency: Today. Button: Mark Now. Tap: the attendance screen. Can snooze. Comes back at midnight. Hide if today is already marked. Shown after a cutoff time. Snoozing does not mark anyone. Someone who can only view tenants still sees Mark Now.

**Who sees it:** Anyone who can view tenants on at least one property you have selected. Owner and admin always. Only if attendance is on.

**How we count:** Show if any selected property has attendance on and has no attendance rows for today. The number on the card is 1, not how many properties and not how many people. Several properties selected: still one card. Add only from properties where attendance is on.

**Missing:** The cutoff time is not named beyond e.g. 10 AM.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Attendance Not Marked Today
- **Source:** New — not yet implemented. Entity: `tenant_attendance`. Cron: `services/cron/attendance.ts` sends WhatsApp reminders but doesn't alert managers. Source type: time-triggered.
- **Access:** `view_tenants`, ANY.
- **Aggregation:** `any`.
- **Calc as written:** Properties where `is_attendance_enabled = 1` and no `tenant_attendance` records exist for today.
- **Visibility leftover:** Shown after a configurable cutoff time (e.g., 10 AM) if attendance hasn't been marked.
- **Tap/filter:** Attendance screen. No filter_code in the registry.
- **Icon:** `attendance.png`.
- **Sort/promotion:** T3, sort by `static`. Promotes To: —.
- **Dismissible:** Yes — midnight reset.
- **Feature gate:** `is_attendance_enabled = 1`.
- **Meaning as written:** Properties with attendance tracking enabled where no attendance has been recorded today.
- **Status leftover:** New.

</details>

---

## E10. Add-on bookings to confirm (Daily Ops)

**Registry status:** New.

**The situation:** They booked laundry, extra meals, or another add-on. The team cannot prepare until you confirm. Only if add-on services are on.

**What the card shows:** **{N} Add-On Booking(s) to Confirm**. N is how many add-on bookings still waiting. Subtitle: Confirm so service can be prepared. Colour: orange. Urgency: none. Button: Confirm Now. Tap: the add-on service bookings list. Can snooze. Comes back at midnight. Hide if 0. Snoozing does not confirm anyone. Someone who can only view tenants still sees Confirm Now.

**Who sees it:** Anyone who can view tenants on at least one property you have selected. Owner and admin always. Only if add-on services are on.

**How we count:** N is pending add-on bookings. Several properties selected: add only from properties where add-on services are on. No ₹ on the title.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Add-On Bookings to Confirm
- **Source:** New — not yet implemented. Entity: `addon_service_booking` with `BookingStatus.PENDING`. Source type: event-driven.
- **Access:** `view_tenants`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(*) FROM addon_service_booking WHERE status = 'pending' AND property_id = ANY(...)`.
- **Tap/filter:** Add-on service bookings list. No filter_code in the registry.
- **Icon:** `addon_service.png`.
- **Sort/promotion:** T2, sort by `count`. Promotes To: —.
- **Dismissible:** Yes — midnight reset.
- **Feature gate:** Add-on services enabled for property.
- **Meaning as written:** Add-on service bookings (laundry, meals, etc.) awaiting manager confirmation.
- **Status leftover:** New.

</details>

---

## E11. Team salary due (Daily Ops)

**Registry status:** New.

**The situation:** Someone on your team's salary is due. This card reminds you. You cannot pay salary from home yet. When you can, this becomes Pay Now. Reimbursement is the Money card. That one can pay.

**What the card shows:** **{N} Team Salary(s) Due**. N is how many unpaid salaries that are due. No ₹ on the title. Subtitle: Pay your team on time. Colour: orange. Urgency: Due. Button: Review Now. Pay Now is leftover. They cannot pay salary from this card. Tap: the team salaries screen. Can snooze. Comes back at midnight. Hide if 0. Snoozing does not pay anyone. Someone who can only view the team still sees Review Now.

**Who sees it:** Anyone who can view the team on at least one property you have selected. Owner and admin always. They may only be able to see the team, not pay them. The button is Review Now because Pay Now would lie.

**How we count:** We count salary. N is unpaid salary rows whose due date is today or earlier. Not reimbursement. Not bonus. No ₹ on the title. Several properties selected: add the numbers together. Reimbursement you can pay from home is [reimbursement due](MONEY.md#a13-reimbursement-due-money). This card is the salary reminder. Two jobs. Do not merge. The old formula also counted reimbursement and bonus (engineering).

**Missing:** bonus is on neither this card nor Money until Pay Now works for it.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Team Salary Due. CTA leftover: Pay Now. Pay Now is not the product button until salary can be paid from home.
- **Source:** New — not yet implemented. Entity: `team_salaries` with `is_paid` and `due_date` fields. Source type: time-triggered.
- **Access:** `view_team`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(*) FROM team_salaries WHERE is_paid = 0 AND due_date <= CURRENT_DATE`. Product counts salary only (`type` = salary). Does not filter type as written.
- **Tap/filter:** Team salaries screen. No filter_code in the registry.
- **Icon:** `team_salary.png`.
- **Sort/promotion:** T2, sort by `days_overdue`. Promotes To: —.
- **Dismissible:** Yes — midnight reset.
- **Cross-reference as written:** Same underlying data as A13 (Staff Salary & Reimbursement Due) in Money. E11 is the salary-only view under Daily Ops; A13 includes all payment types (salary + bonus + reimbursement) under Money. Implementation must deduplicate or differentiate scope.
- **Product (NEED-YOU #3 A+C, 16 Aug 2026):** E11 is a salary reminder until salary can be paid from home. Not reimbursement. Not a second Pay Now for reimbursement. Product button is Review Now. Pay Now is a registry leftover only. A13 is reimbursement due only, and that card keeps Pay Now. Salary and bonus stay off A13 until Pay Now works for them. Not B.
- **Meaning as written:** Staff salaries that are due but not yet paid.
- **Status leftover:** New.

</details>
