# Growth

These cards appear in View All under Growth. Home may show at most one of them at a time in the stack.

AutoPay setup, visits today, cold leads, bot bookings waiting on payment, tenants not on the app.

**This is not the checklist / task / schedule Task module.**

This follows the sourced registry. Live-vs-code is in [GAPS.md](GAPS.md).

These cards are count only. No ₹ on the title. Compact ₹ only when How we count names an amount. None of these cards name one. Ruled [NEED-YOU #5](NEED-YOU.md) A, 16 Aug 2026.

F5 is the Growth card for the same people as [People B9](PEOPLE.md#b9-tenants-to-install-the-app-people). Same list, two names, still not merged. On home they never see both at once. On View All they sit on different chips. Ruled [NEED-YOU People 1](NEED-YOU.md) A, 16 Aug 2026.

---

## F1. Tenants to set up AutoPay (Growth)

**Registry status:** Live on the old home screen. Not on the new homepage yet.

**The situation:** They are staying. They have not set up AutoPay. Every month you chase them for rent. Only if AutoPay is on.

**What the card shows:** **{N} Tenant(s) Without AutoPay**. N is how many staying tenants with no AutoPay. Subtitle: More autopay = less chasing every month. Colour: orange. Urgency: none. Button: Set Up Now. Tap: the tenant list, already filtered to no AutoPay. Can snooze. Comes back at midnight. Hide if 0. Snoozing does not set up AutoPay. Someone who can only view tenants still sees Set Up Now.

**Who sees it:** Anyone who can view tenants on at least one property you have selected. Owner and admin always. Only if AutoPay is on.

**How we count:** N is distinct people staying with you who have no AutoPay row. Several properties selected: add only from properties where AutoPay is on. A bounce after they already set it up is [AutoPay bounced](MONEY.md#a7-autopay-bounced-money). Money that arrived unmatched is [AutoPay needs matching](MONEY.md#a10-autopay-needs-matching-money). This card is people who never set it up. Three jobs. Do not merge.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Tenants to Set Up AutoPay; AutoPay Setup
- **Source:** `getAllTenants.ts:2656` (old system, filter_code: 5011) — `autopay_pending_count` widget count via `.leftJoin('autopay'...)` `COUNT(... ap.id IS NULL)` at :2655-2656, filter_code 5011 emitted at :2744, applied at :4213. Source type: system-detected.
- **Access:** `view_tenants`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(DISTINCT tenant) WHERE status = 1 AND autopay.id IS NULL` (LEFT JOIN on autopay table, no matching row).
- **Tap/filter:** Tenant list filtered to no autopay. Old filter_code: 5011.
- **Icon:** `autopay.png`.
- **Sort/promotion:** T4, sort by `count`. Promotes To: —.
- **Dismissible:** Yes — midnight reset.
- **Feature gate:** Property has autopay enabled.
- **Meaning as written:** Active tenants who don't have an autopay mandate set up.
- **Status leftover:** Live (old system).

</details>

---

## F2. Visits scheduled today (Growth)

**Registry status:** New.

**The situation:** Someone is coming to see a room today. Get the room and the tour ready. A good visit turns into a booking.

**What the card shows:** **{N} Visit(s) Scheduled Today**. N is how many visits still scheduled for today. Subtitle: Prepare rooms for incoming prospects. Colour: green. Urgency: Today. Button: View Visits. Tap: today's visit schedule. Can snooze. Comes back at midnight. Hide if 0. Snoozing does not cancel the visit. Someone who can only view leads still sees View Visits.

**Who sees it:** Anyone who can view leads on at least one property you have selected. Owner and admin always.

**How we count:** N is visit rows dated today with status scheduled. Several properties selected: add the numbers together. A tenant's guest waiting at the gate is [guest visit requests](PEOPLE.md#b11-guest-visit-requests-people). This card is a prospect coming to see a room. Two jobs. Do not merge.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Visits Scheduled Today
- **Source:** New — not yet implemented. Entity: `visits`. Source type: time-triggered.
- **Access:** `view_leads`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(*) FROM visits WHERE visit_date = CURRENT_DATE AND status = 'SCHEDULED'`.
- **Tap/filter:** Today's visit schedule. No filter_code in the registry.
- **Icon:** `visit.png`.
- **Sort/promotion:** T3, sort by `deadline`. Promotes To: —.
- **Dismissible:** Yes — midnight reset.
- **Meaning as written:** Property visits scheduled for today.
- **Status leftover:** New.

</details>

---

## F3. Leads without follow-up (Growth)

**Registry status:** New.

**The situation:** A lead came in more than 48 hours ago. Nobody logged a follow-up. They are going cold. Call them before they book somewhere else.

**What the card shows:** **{N} Lead(s) Without Follow-Up**. N is how many leads with no follow-up after 48 hours. Subtitle: Leads going cold — follow up before they're lost. Colour: orange. Urgency: 48+ hours. Button: Follow Up. Tap: the lead list, already filtered to stale (more than 48 hours). Can snooze. Comes back at midnight. Hide if 0. Snoozing does not log a follow-up. Someone who can only view leads still sees Follow Up.

**Who sees it:** Anyone who can view leads on at least one property you have selected. Owner and admin always.

**How we count:** N is distinct leads older than 48 hours with no later follow-up row. Several properties selected: add the numbers together. A booking sitting too long is [stale bookings](PEOPLE.md#b8-stale-bookings-people). This card is leads, not bookings. Two jobs. Do not merge.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Leads Without Follow-Up
- **Source:** New — not yet implemented. Entities: `tenant` (status=3), `lead_status`. Source type: system-detected.
- **Access:** `view_leads`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(DISTINCT tenant) WHERE status = 3 (lead) AND created_at < (NOW() - INTERVAL '48 hours') AND NOT EXISTS(lead_status WHERE tenant_id = t.id AND created_at > t.created_at)`.
- **Tap/filter:** Lead list filtered to stale (>48 hours). No filter_code in the registry.
- **Icon:** `lead.png`.
- **Sort/promotion:** T4, sort by `days_overdue`. Promotes To: —.
- **Dismissible:** Yes — midnight reset.
- **Meaning as written:** Leads older than 48 hours with no status update or follow-up logged.
- **Status leftover:** New.

</details>

---

## F4. Bot bookings payment pending (Growth)

**Registry status:** New.

**The situation:** They started a booking on the bot. A bed is held. They have not paid. A quick follow-up can close it. Only if the booking bot is on.

**What the card shows:** **{N} Bot Booking(s) Payment Pending**. N is how many bot bookings still unpaid with a bed held. Subtitle: Online bookings with incomplete payment — close them. Colour: orange. Urgency: none. Button: Follow Up. Tap: the bot bookings list. Can snooze. Comes back at midnight. Hide if 0. Snoozing does not collect. Someone who can only view leads still sees Follow Up.

**Who sees it:** Anyone who can view leads on at least one property you have selected. Owner and admin always. Only if the booking bot is on.

**How we count:** N is bot-booking rows with payment not done and a bed reserved. No ₹ on the title. Several properties selected: add only from properties where the booking bot is on. A regular booking with no token at all is [token not collected](MONEY.md#a3-token-not-collected-money). A booking waiting for your yes is [bookings waiting for a yes](PEOPLE.md#b1-bookings-waiting-for-a-yes-people). This card is bot bookings that started and did not pay. Three jobs. Do not merge.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Bot Booking Payments Pending; Bot Bookings
- **Source:** New — not yet implemented. Entity: `booking_bot_users` with `payment_status` field. Source type: system-detected.
- **Access:** `view_leads`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(*) FROM booking_bot_users WHERE payment_status = 0 AND bed_reserved > 0`.
- **Tap/filter:** Bot bookings list. No filter_code in the registry.
- **Icon:** `bot_booking.png`.
- **Sort/promotion:** T4, sort by `count`. Promotes To: —.
- **Dismissible:** Yes — midnight reset.
- **Feature gate:** Booking bot enabled.
- **Meaning as written:** Online bookings through the booking bot with incomplete payment.
- **Status leftover:** New.

</details>

---

## F5. Tenants not on the app (Growth)

**Registry status:** New. Cross-listed with [People B9](PEOPLE.md#b9-tenants-to-install-the-app-people).

**The situation:** They are staying. They are not on the tenant app. Rent, complaints, and bills still go through you by hand. Get them on the app and that work drops.

**What the card shows:** **{N} Tenant(s) Not On App**. N is how many staying tenants with no app. Subtitle: Every tenant off the app = more manual work for you. Colour: blue. Urgency: none. Button: Send Invite. Tap: the tenant list, already filtered to no app. Can snooze. Comes back at midnight. Hide if 0. Snoozing does not send an invite.

**Who sees it:** Anyone who can view tenants on at least one property you have selected. Owner and admin always.

**How we count:** N is distinct people staying with you who have not installed the app. Same people as [People B9](PEOPLE.md#b9-tenants-to-install-the-app-people) **{N} Tenant(s) Without App**. This is the Growth chip: **{N} Tenant(s) Not On App**. You never see both at once. Do not merge names. Ruled [NEED-YOU People 1](NEED-YOU.md) A, 16 Aug 2026. Several properties selected: add the numbers together.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Tenants Not On App
- **Source:** `getAllTenants.ts:3037` (old system, filter_code: 102) — `numCode == 102` sets `onboarding_flag = false`; count at :1855 (`not_on_app` = `onboarding_flag = false AND status = 1`) and :2653 (`app_download_pending_count`). Source type: system-detected.
- **Access:** `view_tenants`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** Same as B9: `COUNT(DISTINCT tenant) WHERE status = 1 AND onboarding_flag = false`.
- **Tap/filter:** Tenant list filtered to no app installed. Old filter_code: 102.
- **Icon:** `app_adoption.png`.
- **Sort/promotion:** T4, sort by `count`. Promotes To: —.
- **Dismissible:** Yes — midnight reset.
- **Cross-reference as written:** Same data as B9 (Tenants to Install App) in People. B9 = People/onboarding framing; F5 = Growth/efficiency framing. Show in the active category's tab only, not both simultaneously. B9 product title: `{N} Tenant(s) Without App`. Card: [B9](PEOPLE.md#b9-tenants-to-install-the-app-people).
- **Product (NEED-YOU People 1 A, 16 Aug 2026):** F5 is the Growth chip. B9 is the People chip for the same list, not a second Growth card. Never both at once. Do not merge names.
- **Operator leftover:** "What percentage of your tenants are still off the app?" Product is a count, not a percentage. Counted title locked NEED-YOU #1 A.
- **Meaning as written:** Active tenants who haven't downloaded the tenant app — distinct from B9 in that this is positioned as a growth metric, not a people/onboarding task.
- **Status leftover:** New (cross-listed with B9).

</details>
