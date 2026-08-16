# People

These cards appear in View All under People. Home may show at most one of them at a time in the stack.

Bookings, move-ins, people leaving, guests, missing phone numbers.

**This is not the checklist / task / schedule Task module.**

This follows the sourced registry. Live-vs-code is in [GAPS.md](GAPS.md).

These cards are count only. No ₹ on the title. Compact ₹ only when How we count names an amount. None of these cards name one. Ruled [NEED-YOU #5](NEED-YOU.md) A, 16 Aug 2026.

---

## B1. Bookings waiting for a yes (People)

**Registry status:** Live on the old home screen. The new homepage copy of this card is switched off. A different joining-request card is live on the new homepage. That one is not this card.

**The situation:** Someone booked a bed. You have not confirmed them yet. Until you say yes or no, the bed is blocked and not occupied.

**What the card shows:** **{N} Move-In Request(s)**. N is how many people waiting for that yes. Subtitle: Review to approve or reject joining. Colour: red. Urgency: none. Button: Approve Joining. Tap: the joining-requests list. Cannot snooze. Hide if 0.

**Who sees it:** Anyone who can view tenants or add tenants on at least one property you have selected. Owner and admin always. Someone who can only view tenants still sees Approve Joining.

**How we count:** N is distinct bookings with no confirmed booking row. Several properties selected: add the numbers together.

**Missing:** A separate joining-request card is already live on the new homepage and is not catalogued here. Do not treat that live card as this one.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Booking Requests to Approve; Move-In Requests
- **Source:** `service.ts:1529` (new system — **now commented-out dead code**; the `move_in_request_pending` push and its `moveInReqRows` query at `service.ts:1382` are disabled), `getAllTenants.ts:3826` (old system, filter_code: 1700 — `numCode == 1700` confirmed-bookings branch; approvals-pending uses its inverse). Source type: event-driven.
- **Access:** `view_tenants, add_tenants`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(DISTINCT tenant) WHERE status = 2 AND NOT EXISTS(tenant_booking_confirmation WHERE is_confirmed = 1)`.
- **Tap/filter:** Joining requests worklist. Old filter_code: 1700 (confirmed-bookings branch; approvals-pending is the inverse).
- **Icon:** `user.png`.
- **Sort/promotion:** T1, sort by `count`. Promotes To: —.
- **Meaning as written:** New joining/booking requests not yet reviewed by the manager.
- **Status leftover:** Live (old system only). Appendix table still says Live (both). Uncatalogued live new-system task: `joining_request_pending` (filter_code 304).

</details>

---

## B2. Move-out requests (People)

**Registry status:** Live on the old home screen and the new homepage. Old and new count them differently.

**The situation:** Someone asked to leave. You have not said yes yet. This is the waiting-for-your-yes card, not the calendar of who is leaving this month, and not the new-flow eviction-request card.

**What the card shows:** **{N} Move-Out Request(s)**. N is how many leave-requests waiting. Subtitle: Review to approve or reject leaving. Colour: red. Urgency: none. Button: Review Now. Tap: the leaving / move-out request list. Cannot snooze. Hide if 0. This is a yes on leaving, not collect.

**Who sees it:** Anyone who can edit eviction on at least one property you have selected. Owner and admin always. That is eviction permission, not invoice permission.

**How we count:** N is leave-requests still waiting. New homepage: this month only. Old home screen: no month cut. Several properties selected: add the numbers together. A property on the new eviction flow uses [B10](#b10-eviction-requests-waiting-people) instead. One property will not show both.

**Missing:** New homepage count is this month only, so an older waiting request can vanish from the number.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Move-Out Requests. CTA leftover: Settle Dues. Subtitle leftover: Collect/adjust dues before they leave.
- **Source:** `service.ts:1542` (new system — `move_out_request_pending`, count query at `service.ts:1417`), `getAllTenants.ts:3839` (old system, filter_code: 120090). Source type: event-driven.
- **Access:** `edit_eviction_access`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** New system: `COUNT(*) FROM tenant_eviction_details WHERE status = 0 AND is_active = 1 AND current month`. Old system: `status = 2 AND is_active = 1`.
- **Tap/filter:** Move-out/eviction worklist. Old filter_code: 120090. Product tap is still this leaving / move-out request list.
- **Icon:** `eviction_request.png`.
- **Sort/promotion:** T1, sort by `deadline`. Promotes To: —.
- **Cross-reference as written:** B2 and B10 share `tenant_eviction_details` but are mutually exclusive: B2 applies to properties NOT on the new eviction flow (status=0 in new system, status=2 in old system); B10 applies only when `is_new_eviction_flow = true` (status=2 in new flow means "requested"). A property will never trigger both.
- **Product (NEED-YOU People 2 B, 16 Aug 2026):** Button is Review Now. Settle Dues is a registry leftover only. This is a yes on leaving, not collect. Do not turn this into a money card. Permission stays eviction.
- **Meaning as written:** Tenants who have active eviction requests pending manager approval.
- **Status leftover:** Live (both systems). Eviction status differs between systems (0 vs 2).

</details>

---

## B3. Eviction extensions (People)

**Registry status:** Live on the old home screen. Not on the new homepage yet.

**The situation:** You already asked them to leave. They asked for more time. You have not said yes or no. Leaving them hanging blocks the next bed.

**What the card shows:** **{N} Extension Request(s) Pending**. N is how many people under notice who asked for more time and are still waiting. Subtitle: Tenants under notice want more time — approve or deny. Colour: orange. Urgency: none. Button: Review Now. Tap: the extension-requests list. Cannot snooze. Hide if 0.

**Who sees it:** Anyone who can edit eviction on at least one property you have selected. Owner and admin always.

**How we count:** N is distinct people under notice who asked for more time and are still waiting. Several properties selected: add the numbers together.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Eviction Extensions to Review; Extension Requests
- **Source:** `getAllTenants.ts:3905` (old system, filter_code: 120093). Source type: event-driven.
- **Access:** `edit_eviction_access`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(DISTINCT tenant) WHERE status = 1 AND tenant_eviction_details.is_active = 1 AND tenant_extension_requests.is_approved = 0`.
- **Tap/filter:** Extension requests list. Old filter_code: 120093.
- **Icon:** `eviction_extension.png`.
- **Sort/promotion:** T2, sort by `deadline`. Promotes To: —.
- **Meaning as written:** Tenants under eviction who have requested a stay extension that hasn't been approved yet.

</details>

---

## B4. Rate departing tenants (People)

**Registry status:** Live on the old home screen. Not on the new homepage yet.

**The situation:** They are leaving. You have not rated them. After they go, you will forget. The rating is for the next property that sees them.

**What the card shows:** **{N} Departing Tenant(s) to Rate**. N is how many people under notice with no rating yet. Subtitle: Rate before they leave — you won't remember later. Colour: blue. Urgency: none. Button: Rate Now. Tap: the tenant list, already filtered to unrated people who are leaving. Can snooze. Comes back at midnight. Hide if 0. Snoozing does not rate anyone.

**Who sees it:** Anyone who can view tenants on at least one property you have selected. Owner and admin always.

**How we count:** N is distinct people under notice with no rating yet. Several properties selected: add the numbers together.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Rate Departing Tenants; Departing Tenants to Rate
- **Source:** `getAllTenants.ts:4190` (old system, filter_code: 5008). Source type: system-detected.
- **Access:** `view_tenants`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(DISTINCT tenant) WHERE status = 1 AND tenant_eviction_details.is_active = 1 AND tenant_eviction_details.rating IS NULL`.
- **Tap/filter:** Tenant list filtered to unrated evictions. Old filter_code: 5008.
- **Icon:** `rating.png`.
- **Sort/promotion:** T4, sort by `count`. Promotes To: —.
- **Dismissible:** Yes — midnight reset.
- **Meaning as written:** Tenants under active eviction who haven't been rated by the manager.

</details>

---

## B5. Keys to collect (People)

**Registry status:** Live on the old home screen. Not on the new homepage yet.

**The situation:** They are leaving. You still need the keys back. Only if key handover is on. Do not let them go without handover.

**What the card shows:** **{N} Key(s) to Collect**. N is how many people under notice at a property where handover is on. Subtitle: Complete handover before tenants leave the property. Colour: orange. Urgency: none. Button: View Handover. Tap: the handover / key-collection list. Can snooze. Comes back at midnight. Hide if 0. Snoozing does not collect a key.

**Who sees it:** Anyone who can do key handover on at least one property you have selected. Owner and admin always. Only if key handover is on.

**How we count:** N is distinct people under notice at a property where key handover is on. Several properties selected: add only from properties where handover is on.

**Missing:** The number is people under notice where handover is on. It does not check whether the keys are already back. It will look like keys still out even after handover is done, until they are no longer under notice.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Keys to Collect
- **Source:** `getAllTenants.ts:3852` (old system, filter_code: 120091). Source type: system-detected.
- **Access:** `key_handover_access`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(DISTINCT tenant) WHERE status = 1 AND tenant_eviction_details.is_active = 1 AND property.complete_handover_enabled = 1`.
- **Tap/filter:** Handover/key collection worklist. Old filter_code: 120091.
- **Icon:** `checklist.png`.
- **Sort/promotion:** T2, sort by `count`. Promotes To: —.
- **Dismissible:** Yes — midnight reset.
- **Feature gate:** `complete_handover_enabled = 1`.
- **Meaning as written:** Tenants under active eviction at properties where key handover is enabled.

</details>

---

## B6. Move-ins this week (People)

**Registry status:** Live on the old home screen. Not on the new homepage yet.

**The situation:** New people are arriving in the next few days. Get the room, keys, and papers ready. This is getting ready, not a mess to fix. Miss it and the first day goes badly.

**What the card shows:** **{N} Move-In(s) This Week**. N is how many bookings arriving in that window. Subtitle: Prepare rooms and onboarding docs. Colour: green. Urgency: This week. Button: View Bookings. Tap: the booking list, already filtered to this week's move-ins. Can snooze. Comes back at midnight. Hide if 0. Snoozing does not prepare the room.

**Who sees it:** Anyone who can view tenants on at least one property you have selected. Owner and admin always.

**How we count:** N is distinct bookings whose joining date is after today and within the next 7 days. Not a Monday–Sunday week. Several properties selected: add the numbers together.

**Missing:** Joining today is outside the formula (`date_of_joining > today`). The title says this week. The count is the next 7 days, and not today.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Move-Ins This Week
- **Source:** `getAllTenants.ts:3786` (old system, filter_code: 1504). Source type: time-triggered.
- **Access:** `view_tenants`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(DISTINCT tenant) WHERE status = 2 AND date_of_joining > today AND date_of_joining <= (today + 7 days)`.
- **Tap/filter:** Booking list filtered to this week's move-ins. Old filter_code: 1504.
- **Icon:** `key.png`.
- **Sort/promotion:** T3, sort by `deadline`. Promotes To: —.
- **Dismissible:** Yes — midnight reset.
- **Meaning as written:** Bookings with a joining date within the next 7 days.

</details>

---

## B7. Bookings without a room (People)

**Registry status:** Live on the old home screen. Not on the new homepage yet.

**The situation:** They booked. You have not told them which room. Assign it before move-in day or the first day is chaos.

**What the card shows:** **{N} Booking(s) Without Room**. N is how many bookings with no room yet. Subtitle: Assign rooms before move-in day. Colour: orange. Urgency: none. Button: Assign Rooms. Tap: the booking list, already filtered to unassigned rooms. Can snooze. Comes back at midnight. Hide if 0. Snoozing does not assign a room.

**Who sees it:** Anyone who can view tenants or view rooms on at least one property you have selected. Owner and admin always. Someone who can only view tenants still sees Assign Rooms.

**How we count:** N is distinct bookings with no room yet. Several properties selected: add the numbers together. A booking with no room that has also sat more than 7 days can show on [stale bookings](#b8-stale-bookings-people) as well. Two jobs: this card assigns the room. That card asks you to convert or cancel.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Bookings Without Room
- **Source:** `getAllTenants.ts:3946` (old system, filter_code: 4003). Source type: system-detected.
- **Access:** `view_tenants, view_room`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(DISTINCT tenant) WHERE status = 2 AND room IS NULL`.
- **Tap/filter:** Booking list filtered to unassigned rooms. Old filter_code: 4003.
- **Icon:** `user.png`.
- **Sort/promotion:** T2, sort by `count`. Promotes To: —.
- **Dismissible:** Yes — midnight reset.
- **Meaning as written:** Bookings that haven't been assigned to a specific room yet.

</details>

---

## B8. Stale bookings (People)

**Registry status:** New.

**The situation:** A booking has sat more than a week. It is blocking a bed and not paying rent. Convert them or cancel so the bed is free.

**What the card shows:** **{N} Stale Booking(s)**. N is how many unconfirmed bookings older than 7 days. Subtitle: Bookings older than 7 days — convert or cancel to free beds. Colour: orange. Urgency: Older than 7 days. Button: Review Now. Tap: the booking list, already filtered to stale (more than 7 days). Can snooze. Comes back at midnight. Hide if 0. After 14 days it jumps up the list. Snoozing does not convert or cancel anyone.

**Who sees it:** Anyone who can view tenants on at least one property you have selected. Owner and admin always.

**How we count:** N is distinct bookings created more than 7 days ago, still not confirmed. Several properties selected: add the numbers together. A stale booking with no room can also show on [bookings without a room](#b7-bookings-without-a-room-people).

<details>
<summary>For engineering</summary>

- **Registry also listed:** Stale Bookings
- **Source:** New — not yet implemented. Uses existing `tenant` table with date filter. Source type: system-detected.
- **Access:** `view_tenants`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(DISTINCT tenant) WHERE status = 2 AND created_at < (CURRENT_DATE - INTERVAL '7 days') AND NOT EXISTS(tenant_booking_confirmation WHERE is_confirmed = 1)`.
- **Tap/filter:** Booking list filtered to stale (>7 days). No filter_code in the registry.
- **Icon:** `stale_booking.png`.
- **Sort/promotion:** T3, sort by `days_overdue`. Promotes To: T2 when any booking > 14 days stale.
- **Dismissible:** Yes — midnight reset.
- **Meaning as written:** Bookings older than 7 days that haven't been confirmed or converted to active tenants.

</details>

---

## B9. Tenants to install the app (People)

**Registry status:** Live on the old home screen. Not on the new homepage yet.

**The situation:** They are staying with you. They do not have the tenant app. Without it they cannot pay online, raise a complaint, or see a bill. Every person off the app is more work by hand.

**What the card shows:** **{N} Tenant(s) Without App**. N is how many staying tenants with no app. Subtitle: Nudge them to install — saves you manual work. Colour: blue. Urgency: none. Button: Send Invite. Tap: the tenant list, already filtered to no app. Can snooze. Comes back at midnight. Hide if 0. Snoozing does not send an invite.

**Who sees it:** Anyone who can view tenants on at least one property you have selected. Owner and admin always.

**How we count:** N is distinct people staying with you who have not installed the app. Several properties selected: add the numbers together. This is the People chip: **{N} Tenant(s) Without App**. Growth shows **{N} Tenant(s) Not On App** for the same people ([F5](GROWTH.md#f5-tenants-not-on-the-app-growth)). You never see both at once. Do not merge names. Ruled [NEED-YOU People 1](NEED-YOU.md) A, 16 Aug 2026.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Tenants to Install the App; Tenants to Install App
- **Source:** `getAllTenants.ts:3037` (old system, filter_code: 102). Source type: system-detected.
- **Access:** `view_tenants`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(DISTINCT tenant) WHERE status = 1 AND onboarding_flag = false`.
- **Tap/filter:** Tenant list filtered to no app installed. Old filter_code: 102.
- **Icon:** `app_download.png`.
- **Sort/promotion:** T4, sort by `count`. Promotes To: —.
- **Dismissible:** Yes — midnight reset.
- **Cross-reference as written:** Same data as F5 (Tenants Not On App) in Growth. B9 = People/onboarding framing; F5 = Growth/efficiency framing. Show in the active category's tab only, not both simultaneously. Same formula. F5 product title: `{N} Tenant(s) Not On App`. Card: [F5](GROWTH.md#f5-tenants-not-on-the-app-growth).
- **Product (NEED-YOU People 1 A, 16 Aug 2026):** B9 is the People chip. F5 is the Growth chip for the same list, not a second People card. Never both at once. Do not merge names.
- **Meaning as written:** Active tenants who haven't downloaded the RentOk tenant app.

</details>

---

## B10. Eviction requests waiting (People)

**Registry status:** New.

**The situation:** On the new eviction flow, someone raised an eviction. You have not approved it. The leave process does not start until you do. This is not the old-flow move-out-request card, and not the calendar of who is leaving this month.

**What the card shows:** **{N} Eviction Request(s) Pending**. N is how many new-flow requests waiting. Subtitle: Approve or reject before move-out process stalls. Colour: red. Urgency: none. Button: Review Now. Tap: the eviction-requests list. Cannot snooze. Hide if 0.

**Who sees it:** Anyone who can edit eviction on at least one property you have selected. Owner and admin always. Only if the new eviction flow is on.

**How we count:** N is new-flow eviction requests still waiting. Several properties selected: add only from properties where the new eviction flow is on. A property not on that flow uses [move-out requests](#b2-move-out-requests-people) instead. One property will not show both.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Eviction Requests Pending Approval; Eviction Requests
- **Source:** `getAllTenants.ts:3839` (old system, filter_code: 120090; `is_new_eviction_flow` branch at `getAllTenants.ts:2021`). Status line still says New. Source type: event-driven.
- **Access:** `edit_eviction_access`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(*) FROM tenant_eviction_details WHERE status = 2 AND is_active = 1` — status 2 = requested (new flow).
- **Tap/filter:** Eviction requests worklist. Old filter_code: 120090 (shared pointer with B2 old system; new-flow branch at :2021).
- **Icon:** `eviction_request.png`.
- **Sort/promotion:** T2, sort by `count`. Promotes To: —.
- **Feature gate:** `is_new_eviction_flow = true`.
- **Meaning as written:** Tenants whose eviction has been requested but not yet approved by the manager (new eviction flow).

</details>

---

## B11. Guest visit requests (People)

**Registry status:** New.

**The situation:** A tenant registered a guest. You have not said yes or no. Approve or decline before the guest is at the gate.

**What the card shows:** **{N} Guest Visit Request(s)**. N is how many guest visits still pending. Subtitle: Approve before the guest arrives at the gate. Colour: orange. Urgency: none. Button: Review Now. Tap: the guest-visit requests list. Can snooze. Comes back at midnight. Hide if 0. Snoozing does not approve anyone.

**Who sees it:** Anyone who can view tenants on at least one property you have selected. Owner and admin always. Only if guest visits are on. Someone who can only view tenants still sees Review Now.

**How we count:** N is `host_friend` rows with status pending. Several properties selected: add only from properties where guest visits are on.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Guest Visit Requests Pending; Guest Visit Requests
- **Source:** New — entity: `host_friend.ts` with status enum ['pending', 'approved', 'declined']. Source type: event-driven.
- **Access:** `view_tenants`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(*) FROM host_friend WHERE status = 'pending' AND property_id = ANY(...)`.
- **Tap/filter:** Guest visit requests list. No filter_code in the registry.
- **Icon:** `guest_visit.png`.
- **Sort/promotion:** T2, sort by `deadline`. Promotes To: —.
- **Dismissible:** Yes — midnight reset.
- **Feature gate:** `tenant_app_host_friends` property setting enabled.
- **Meaning as written:** Tenants who have registered guest visits waiting for manager approval.

</details>

---

## B12. Move-outs this month (People)

**Registry status:** New.

**The situation:** People are leaving this month. Get ready: collect what they still owe, finish move-out checks, return deposit, make the room ready. This is the calendar. It is not a request waiting for your yes.

**What the card shows:** **{N} Move-Out(s) This Month**. N is how many people with a leave date in this month. Subtitle: Prepare rooms, collect dues, process refunds. Colour: green. Urgency: This month. Button: View List. Tap: the tenant list, already filtered to move-outs this month. Can snooze. Comes back at midnight. Hide if 0. Snoozing does not collect dues or free the room.

**Who sees it:** Anyone who can view tenants on at least one property you have selected. Owner and admin always.

**How we count:** N is distinct people still staying, under notice, whose leave date falls in this calendar month. Several properties selected: add the numbers together. Someone waiting for your yes on leaving is [move-out requests](#b2-move-out-requests-people) or [eviction requests waiting](#b10-eviction-requests-waiting-people). This card is who is dated to leave this month.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Move-Outs This Month
- **Source:** `getAllTenants.ts:3424` (old system, filter_code: 610). Status line still says New. Source type: time-triggered.
- **Access:** `view_tenants`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(DISTINCT tenant) WHERE status = 1 AND date_of_eviction >= FIRST_OF_MONTH AND date_of_eviction <= LAST_OF_MONTH AND tenant_eviction_details.is_active = 1`.
- **Tap/filter:** Tenant list filtered to move-outs this month (filter_code: 610).
- **Icon:** `move_out.png`.
- **Sort/promotion:** T3, sort by `deadline`. Promotes To: —.
- **Dismissible:** Yes — midnight reset.
- **Meaning as written:** Tenants with move-out dates falling within the current month.

</details>

---

## B13. Tenants without contact info (People)

**Registry status:** New.

**The situation:** They are staying with you. There is no usable phone number. Rent reminders, WhatsApp, and payment links will not reach them.

**What the card shows:** **{N} Tenant(s) Without Contact**. N is how many staying tenants with no usable phone. Subtitle: No phone number — blocks reminders and payment links. Colour: orange. Urgency: none. Button: Add Contact. Tap: the tenant list, already filtered to missing contact. Can snooze. Comes back at midnight. Hide if 0. Snoozing does not add a number.

**Who sees it:** Anyone who can view tenants or edit tenants on at least one property you have selected. Owner and admin always. Someone who can only view tenants still sees Add Contact.

**How we count:** N is distinct people staying with you whose phone is empty or not 10 digits. Several properties selected: add the numbers together.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Tenants Without Contact Info; Contact Info Missing
- **Source:** `getAllTenants.ts:3182` (old system, filter_code: 305). Status line still says New. Source type: system-detected.
- **Access:** `view_tenants, edit_tenants`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(DISTINCT tenant) WHERE status = 1 AND (phone IS NULL OR phone = '' OR LENGTH(phone) != 10)`.
- **Tap/filter:** Tenant list filtered to missing contact (filter_code: 305).
- **Icon:** `contact_missing.png`.
- **Sort/promotion:** T4, sort by `count`. Promotes To: —.
- **Dismissible:** Yes — midnight reset.
- **Meaning as written:** Active tenants without a valid phone number on file.

</details>
