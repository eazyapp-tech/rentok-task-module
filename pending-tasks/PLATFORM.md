# Platform

These cards appear in View All under Platform. Home may show at most one of them at a time in the stack.

RentOk plan ending soon, WhatsApp balance low. Eqaro insurance is deprecated and does not show.

**This is not the checklist / task / schedule Task module.**

This follows the sourced registry. Live-vs-code is in [GAPS.md](GAPS.md).

Counted title when N exists. Presence only when there is no N. Compact ₹ only when How we count names an amount. None of these cards name one. Ruled [NEED-YOU #5](NEED-YOU.md) A, 16 Aug 2026.

G3 is deprecated. It is not a live home card.

---

## G1. RentOk plan expiring soon (Platform)

**Registry status:** New.

**The situation:** Your RentOk plan ends within 7 days. Renew before it ends. After that, features get restricted.

**What the card shows:** **Plan Expiring in {N} Days**. N is how many days are left. Subtitle: Renew to avoid service disruption. Colour: red. Urgency: `Expires in {N} days`. Button: Renew Now. Tap: the billing / subscription screen. Cannot snooze. Hide unless a plan ends within 7 days.

**Who sees it:** Everyone with app access. The job is for owner and admin. Others see Renew Now so they know, even if they cannot renew.

**How we count:** Show if any selected property's RentOk plan ends within 7 days. N is days left on the plan that ends soonest. The number on the card is days, not how many properties. Several properties selected: still one card. Show if any match. Already-due RentOk charges are [RentOk charges due](MONEY.md#a9-rentok-charges-due-money). This card is the plan ending soon. Two jobs. Do not merge.

**Missing:** which property's days go on the title when several are selected. Aggregation is any, not a count of plans.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Plan Expiring Soon
- **Source:** New — not yet implemented. Entity: `property_plan` with `end_date`. Also: `rentokExpiringPlan.is_active` checked in `services/entryExit/entryExitService.ts:248-259`. Source type: time-triggered.
- **Access:** `—` (universal), ANY.
- **Aggregation:** `any`.
- **Calc as written:** `COUNT(*) FROM property_plan WHERE end_date BETWEEN CURRENT_DATE AND (CURRENT_DATE + INTERVAL '7 days')`. Product N is days remaining, not that count. Count leftover.
- **Visibility as written:** Shown when plan expiry is within 7 days.
- **Tap/filter:** Billing/subscription screen. No filter_code in the registry.
- **Icon:** `plan_expiry.png`.
- **Sort/promotion:** T1, sort by `deadline`. Promotes To: —.
- **Dismissible:** No.
- **Feature gate:** None.
- **Meaning as written:** RentOk subscription plan expiring within 7 days.
- **Operator leftover:** "Is your RentOk plan about to expire?" Product is the counted days title.
- **Phase leftover:** Not listed in parent implementation phases. G2 is Phase 4.
- **Status leftover:** New.

</details>

---

## G2. WhatsApp balance low (Platform)

**Registry status:** New.

**The situation:** WhatsApp messages are running low. When they hit zero, rent reminders, complaint alerts, and move-in messages stop going out. Recharge before you go silent. Only if WhatsApp is on.

**What the card shows:** **WhatsApp Balance Low**. Presence only. No count and no ₹ on the title. Subtitle: Only {N} messages left — recharge to keep reminders flowing. Colour: orange. Urgency: `{N} messages remaining`. Button: Recharge Now. Tap: the WhatsApp recharge screen. Can snooze. Comes back at midnight. Hide unless remaining messages are below the low-balance line. Hide if WhatsApp is off. When the balance hits 0 it jumps up the list. Snoozing does not recharge. Someone who can only view tenants still sees Recharge Now.

**Who sees it:** Anyone who can view tenants on at least one property you have selected. Owner and admin always. Only if WhatsApp is on.

**How we count:** Show if any selected property's WhatsApp balance is below the low-balance line. The title is presence. Remaining messages sit on the subtitle and the urgency line. Count or presence only. No ₹. Several properties selected: show if any is low. Only from properties where WhatsApp is on. KYC credits running low is [C8](COMPLIANCE.md#c8-kyc-credits-running-low-compliance). This card is WhatsApp messages. Two jobs. Do not merge.

**Missing:** which property's remaining messages go on the subtitle when several are selected. Aggregation is any, not a sum of messages.

<details>
<summary>For engineering</summary>

- **Registry also listed:** WhatsApp Balance Low
- **Source:** New — not yet implemented. Requires balance calculation across recharge and usage tables. Source type: system-detected.
- **Access:** `view_tenants`, ANY.
- **Aggregation:** `any`.
- **Calc as written:** WhatsApp message balance below threshold (configurable, e.g., < 50 messages). Product does not lock 50.
- **Visibility as written:** Shown when balance < threshold.
- **Tap/filter:** WhatsApp recharge screen. No filter_code in the registry.
- **Icon:** `whatsapp.png`.
- **Sort/promotion:** T3, sort by `count` (presence; shown count is 1). Promotes To: T1 when balance = 0. See [RULES](RULES.md#promotion-a-card-can-jump-a-tier).
- **Dismissible:** Yes — midnight reset.
- **Feature gate:** WhatsApp enabled.
- **Meaning as written:** WhatsApp message credits are running low.
- **Operator leftover:** "Are you running out of WhatsApp message credits?"
- **Status leftover:** New.

</details>

---

## G3. Eqaro insurance (Platform)

**Registry status:** Deprecated. Does not show on the home screen.

**What it was:** Pending insurance claims through Eqaro (also called Ikaro). That integration shut down in 2025.

**What the card shows:** Nothing. You will not see this card. Old claims may still sit in historical data. That leftover is not a job on this feed.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Insurance Claims Pending; Eqaro Insurance
- **Source:** Deprecated — Eqaro/Ikaro integration shut down. Entity references may still exist in codebase. Source type was system-detected.
- **Visibility as written:** Not shown — deprecated.
- **Priority colour / tier / sort / access / dismissible / title / subtitle / CTA / destination / icon / urgency / feature gate:** N/A
- **Aggregation:** N/A
- **Calc as written:** N/A — integration deprecated.
- **Operator leftover:** "Do you have pending insurance claims?" Existing claims from before deprecation may still appear in historical data. Not a home-screen card.
- **Meaning as written:** Pending insurance claims via Eqaro/Ikaro integration.
- **Status leftover:** Deprecated — Eqaro/Ikaro integration shut down (2025). Kept in registry for historical reference. Do not reopen as live.

</details>
