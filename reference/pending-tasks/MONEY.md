# Money

These cards appear in View All under Money. Home may show at most one of them at a time in the stack.

Collections, refunds, settlements, payouts, RentOk charges.

**This is not the checklist / task / schedule Task module.**

This follows the sourced registry. Live-vs-code is in [GAPS.md](GAPS.md).

Home shows compact ₹ (₹45K). Full amount after they tap. See [RULES](RULES.md#rupees-on-the-card-face). Ruled [NEED-YOU #4](NEED-YOU.md) B, 16 Aug 2026. Compact ₹ only when How we count names the amount. Count or presence only until then. Ruled [NEED-YOU #5](NEED-YOU.md) A, 16 Aug 2026.

---

## A1. Rent overdue (Money)

**Registry status:** Live on the old home screen. Not on the new homepage yet.

**The situation:** Someone still staying with you has unpaid rent more than 30 days late. You open the home screen for the chronic cases, not the full dues list.

**What the card shows:** **{N} Rent Dues Overdue — ₹{amount}**. N is how many tenants. ₹ is every unpaid bill those same people have, not rent alone. Subtitle: Follow up now — these tenants are 30+ days past due. Colour: red. Urgency: Overdue 30+ days. Button: Collect Dues. Tap: the tenant list, already filtered to people late on rent. Cannot snooze. Hide if 0.

**Who sees it:** Anyone who can view invoices on at least one property you have selected. Owner and admin always.

**How we count:** N is tenants with unpaid rent more than 30 days. ₹ is all unpaid bills for those same tenants, not only rent. Several properties selected: add the numbers together.

**Rupees on home:** compact (₹45K). Full amount after tap. Ruled [NEED-YOU #4](NEED-YOU.md) B, 16 Aug 2026.

**Live vs this claim:** Tap and amount disagree with the card. See [GAPS.md](GAPS.md).

<details>
<summary>For engineering</summary>

- **Registry also listed:** Rent & Bills Overdue — ₹{amount}
- **Source:** `getAllTenants.ts:2670` (old system). Not yet in new homepage system. Source type: system-detected.
- **Access:** `view_invoices`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** Count: `COUNT(DISTINCT tenant) WHERE status = 1 AND invoice.status = 0 (unpaid) AND due_type = 'Rent' AND (today - due_date) > 30 days`. Amount in title: `SUM(all unpaid invoices)` for the same tenants (all due types, not just rent).
- **Tap/filter:** Tenant list filtered to rent defaulters (filter_code: 5001).
- **Icon:** `bills.png`.
- **Sort/promotion:** T1, sort by `amount`. Promotes To: — (already T1).
- **Meaning as written:** Count of tenants with rent invoices unpaid for more than 30 days. Title includes the total unpaid amount across all invoice types.

</details>

---

## A2. Payments not linked (Money)

**Registry status:** Live on the new homepage.

**The situation:** Money hit the bank. It is not matched to a tenant or a bill yet. Your books will not line up until you link it.

**What the card shows:** **{N} Transaction(s) Not Linked**. N is how many unlinked payments. No rupees on the title. Subtitle: Match them to avoid reconciliation gaps. Colour: orange. Urgency: none. Button: Link Now. Tap: the list of payments not linked to tenants. Can snooze. Comes back at midnight. Hide if 0. Snoozing does not link the money.

**Who sees it:** Anyone who can view invoices or record a payment on at least one property you have selected. Owner and admin always.

**How we count:** N is unlinked transactions. No ₹ on the title. Several properties selected: add the numbers together.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Payments Not Linked; Transactions Not Linked
- **Source:** `getPendingTasks() in v1/homepage/service.ts` — query ~L1371, card push ~L1584 (new system). Source type: system-detected.
- **Access:** `view_invoices, record_payment`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(*) FROM invoices WHERE status = 1 AND is_active = 1 AND payer LIKE 'cust_%'`.
- **Tap/filter:** List of payments not linked with tenants. No filter_code in the registry.
- **Icon:** `payment_link.png`.
- **Sort/promotion:** T2, sort by `count`. Promotes To: —.
- **Dismissible:** Yes — midnight reset.
- **Meaning as written:** Bank transactions received but not yet matched to a tenant or bill.

</details>

---

## A3. Token not collected (Money)

**Registry status:** Live on the old home screen. Not on the new homepage yet.

**The situation:** A booking has no paid invoice at all. No token, no advance. Collect before move-in, or the booking is a name without money.

**What the card shows:** **{N} Token Payment(s) to Collect**. N is how many bookings. No rupees on the title. Subtitle: No advance received — collect before move-in. Colour: red. Urgency: none. Button: Collect Now. Tap: the booking list, already filtered to unpaid tokens. Cannot snooze. Hide if 0.

**Who sees it:** Anyone who can view invoices or view tenants on at least one property you have selected. Owner and admin always. Someone who can only view tenants still sees Collect Now.

**How we count:** N is bookings (distinct people) with no paid invoice at all. Not a token due type: any paid bill. No ₹ on the title. Several properties selected: add the numbers together.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Token Payments to Collect
- **Source:** `getAllTenants.ts:2954` (old system). Source type: system-detected.
- **Access:** `view_invoices, view_tenants`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(DISTINCT tenant) WHERE status = 2 AND NOT EXISTS(invoice WHERE payer = tenant.firebase_id AND status = 1)`.
- **Tap/filter:** Booking list filtered to unpaid tokens (filter_code: 7003).
- **Icon:** `money.png`.
- **Sort/promotion:** T1, sort by `count`. Promotes To: —.
- **Meaning as written:** Bookings that don't have any paid invoice on record.

</details>

---

## A4. Ex-tenant dues (Money)

**Registry status:** Live on the old home screen. Not on the new homepage yet.

**The situation:** They already left. They still owe you. The longer you wait, the harder it is to recover.

**What the card shows:** **{N} Final Due(s) to Collect — ₹{amount}**. N is how many people who already left. ₹ is the unpaid amount as written below. Subtitle: Ex-tenants with outstanding balances — recover before losing contact. Colour: red. Urgency: none. Button: Collect Dues. Tap: the old tenants list, already filtered to outstanding dues. Cannot snooze. Hide if 0.

**Who sees it:** Anyone who can view invoices on at least one property you have selected. Owner and admin always.

**How we count:** N is people who already left and still have unpaid invoices. ₹ is the sum written in calc. Several properties selected: add the numbers together.

**Rupees on home:** compact (₹45K). Full amount after tap. Ruled [NEED-YOU #4](NEED-YOU.md) B, 16 Aug 2026.

**Missing:** The rupee sum is written as invoices in one status, without repeating the "already left" filter that the count uses. The count and the rupees may not be the same people.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Final Dues to Collect — ₹{amount}
- **Source:** `getAllTenants.ts:2850` (old system). Source type: system-detected.
- **Access:** `view_invoices`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** Count: `COUNT(DISTINCT tenant) WHERE status = 0 AND invoice.status = 4`. Amount: `SUM(invoice.amount) WHERE status = 4`.
- **Tap/filter:** Old Tenants list filtered to outstanding dues (filter_code: 3001).
- **Icon:** `money_copy.png`.
- **Sort/promotion:** T1, sort by `amount`. Promotes To: —.
- **Meaning as written:** Old/evicted tenants who still have unpaid invoices.

</details>

---

## A5. Deposits to refund (Money)

**Registry status:** Live on the old home screen. Not on the new homepage yet.

**The situation:** They already left. You are still holding their deposit. You return what is left after deductions. Delays become fights.

**What the card shows:** **{N} Deposit(s) to Refund — ₹{amount}**. N is people who already left with money still to return. ₹ is the sum of those balances. Subtitle: Process refunds before tenants escalate. Colour: orange. Urgency: none. Button: Review Refunds. Tap: the old tenants list, Deposit Refund Pending. Can snooze. Comes back at midnight. Hide if 0. Snoozing does not refund anyone.

**Who sees it:** Anyone who can add a refund on at least one property you have selected. Owner and admin always.

**How we count:** N is people who already left where the refundable amount is more than 0. ₹ is the sum of those amounts. Several properties selected: add the numbers together.

**Rupees on home:** compact (₹45K). Full amount after tap. Ruled [NEED-YOU #4](NEED-YOU.md) B, 16 Aug 2026.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Deposits to Refund — ₹{amount}
- **Source:** `getAllTenants.ts:2857`, helper: `getAllTenants.ts:2984` (old system). Source type: system-detected.
- **Access:** `add_refund_access`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** Count: number of old tenants where `refundable_amount > 0` (computed via `getRefundableAmount()` — deposits paid minus refunds issued minus deposit adjustments). Amount: sum of all positive refundable amounts.
- **Tap/filter:** Old Tenants list > Deposit Refund Pending (filter_code: 3002).
- **Icon:** `money-arrow.png`.
- **Sort/promotion:** T2, sort by `amount`. Promotes To: —.
- **Dismissible:** Yes — midnight reset.
- **Meaning as written:** Old tenants who have a positive refundable deposit balance.

</details>

---

## A6. Bank payout failed (Money)

**Registry status:** New.

**The situation:** Tenants paid. The payout to your bank failed. Collected rent is stuck until you fix bank details. This is settlement, not the wallet payout card.

**What the card shows:** **{N} Settlement(s) Failed**. N is how many failed payouts. No ₹ until we can name the number. Subtitle: Payouts to your bank failed — update bank details. Colour: red. Urgency: Action needed. Button: Fix Now. Tap: the settlement list, already filtered to failed. Cannot snooze. Hide if 0.

**Who sees it:** Anyone who can access bank details on at least one property you have selected. Owner and admin always.

**How we count:** N is failed settlement rows. Count only. No ₹ on the title. Several properties selected: add the counts together.

**Missing:** the amount formula. Do not show a rupee figure we cannot define. Ruled [NEED-YOU #5](NEED-YOU.md) A, 16 Aug 2026.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Settlement Failed; `{N} Settlement(s) Failed — ₹{amount}` (amount leftover; product title is count only)
- **Source:** New — not yet implemented. Entity: `settlement_scheduler` with status field. Source type: system-detected.
- **Access:** `bank_access`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(*) FROM settlement_scheduler WHERE status = 3 (failure) AND is_active = 1`. No amount formula.
- **Tap/filter:** Settlement list filtered to failed. No filter_code in the registry.
- **Icon:** `settlement_failed.png`.
- **Sort/promotion:** T1, sort by `count`. Registry leftover: sort by `amount`. Ruled NEED-YOU #5 A, 16 Aug 2026.
- **Meaning as written:** Bank payout attempts that failed — money collected from tenants but not transferred to the operator's bank.

</details>

---

## A7. AutoPay bounced (Money)

**Registry status:** New.

**The situation:** Auto-collect tried and bounced. Follow up by hand or that rent stays uncollected.

**What the card shows:** **{N} AutoPay Debit(s) Failed**. Subtitle: Auto-collect bounced — follow up with these tenants. Colour: red. Urgency: none. Button: Follow Up. Tap: the AutoPay list, already filtered to failed debits. Cannot snooze. Hide if 0.

**Who sees it:** Anyone who can view invoices on at least one property you have selected. Owner and admin always. Only if AutoPay is on.

**How we count:** N is failed or exhausted-retry debit rows. No ₹ on the title. Several properties selected: add the numbers together. Only from properties where AutoPay is on.

<details>
<summary>For engineering</summary>

- **Registry also listed:** AutoPay Debits Failed
- **Source:** New — not yet implemented. Entity: `autopay_debit_schedule.ts` — numeric status enum `AutopayDebitScheduleStatus`, `retry_count` field (line 49). Source type: system-detected.
- **Access:** `view_invoices`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(*) FROM autopay_debit_schedule WHERE status = 3 (SKIPPED — retries exhausted) OR (status = 0 (PENDING) AND retry_count >= 3)`. Status enum: 0=PENDING, 1=PROCESSING, 2=RESOLVED, 3=SKIPPED, 4=INTERIM. Retry cap is hardcoded to 3, not a column.
- **Tap/filter:** AutoPay list filtered to failed debits. No filter_code in the registry.
- **Icon:** `autopay_failed.png`.
- **Sort/promotion:** T1, sort by `count`. Promotes To: —.
- **Feature gate:** Property must have autopay enabled.
- **Meaning as written:** Automatic rent collection attempts that failed after retries.

</details>

---

## A8. Wallet payout failed (Money)

**Registry status:** New.

**The situation:** Wallet payout to your bank failed. Usually a bank-detail mismatch. Collected money is stuck until you fix it. This is the wallet / FlexiPe card, not the settlement card.

**What the card shows:** **{N} Payout(s) Failed**. Subtitle: Bank transfer failed — verify your account details. Colour: red. Urgency: Action needed. Button: Fix Now. Tap: the wallet payouts screen. Cannot snooze. Hide if 0.

**Who sees it:** Anyone who can access bank details on at least one property you have selected. Owner and admin always. Only if Wallet is on.

**How we count:** N is failed wallet payout rows. No ₹ on the title. Several properties selected: add the numbers together. Only from properties where Wallet is on.

**Missing:** the exact property setting name. Written as FlexiPe / Wallet on, or wallet on.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Wallet Payout Failed; Payout Failed
- **Source:** New — not yet implemented. Entity: `wallet_payouts` with status = -1 for failure. Source type: system-detected.
- **Access:** `bank_access`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(*) FROM wallet_payouts WHERE status = -1 AND is_active = 1`.
- **Tap/filter:** Wallet payouts screen. No filter_code in the registry.
- **Icon:** `payout_failed.png`.
- **Sort/promotion:** T1, sort by `count`. Promotes To: —.
- **Feature gate:** FlexiPe/Wallet enabled.
- **Meaning as written:** FlexiPe/wallet payout to operator's bank that failed.

</details>

---

## A9. RentOk charges due (Money)

**Registry status:** New.

**The situation:** RentOk's own charges are due. If unpaid, features may get restricted.

**What the card shows:** **RentOk Charges Due**. Presence only. No count and no ₹ on the title. Subtitle: RentOk subscription pending — avoid service disruption. Colour: red. Urgency: Due now. Button: Pay Now. Tap: the billing / subscription screen. Cannot snooze. Hide if 0.

**Who sees it:** Everyone with app access. The job is for owner and admin. Others see Pay Now so they know, even if they cannot pay.

**How we count:** Show if any selected property matches. The number on the card is 1, not how many properties. Count or presence only. No ₹. Do not sort as if there were an amount.

**Missing:** the amount formula. Ruled [NEED-YOU #5](NEED-YOU.md) A, 16 Aug 2026.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Platform Charges Due. Sort leftover: `amount`.
- **Source:** New — not yet implemented. Entity: `rentok_charges_scheduler`. Source type: time-triggered.
- **Access:** `—` (universal). Access logic still listed as ANY.
- **Aggregation:** `any`.
- **Calc as written:** `COUNT(*) FROM rentok_charges_scheduler WHERE status IN (0, 1) AND is_active = 1 AND start_date <= CURRENT_DATE`. No amount formula.
- **Tap/filter:** Billing/subscription screen. No filter_code in the registry.
- **Icon:** `platform_charges.png`.
- **Sort/promotion:** T1, sort by `count` (presence; shown count is 1). Registry leftover: sort by `amount`. Ruled NEED-YOU #5 A, 16 Aug 2026.
- **Meaning as written:** RentOk subscription or platform charges that are due.

</details>

---

## A10. AutoPay needs matching (Money)

**Registry status:** New.

**The situation:** AutoPay money arrived. The system could not tell which tenant or bill it belongs to. You match it by hand.

**What the card shows:** **{N} AutoPay Payment(s) to Review**. Subtitle: Money received but not matched — link to the right tenant. Colour: orange. Urgency: none. Button: Review Now. Tap: AutoPay transactions already filtered to manual review. Can snooze. Comes back at midnight. Hide if 0. Snoozing does not match the money.

**Who sees it:** Anyone who can view invoices or record a payment on at least one property you have selected. Owner and admin always. Only if AutoPay is on.

**How we count:** N is rows in manual review. No ₹ on the title. Several properties selected: add the numbers together. Only from properties where AutoPay is on.

<details>
<summary>For engineering</summary>

- **Registry also listed:** AutoPay Manual Review; AutoPay Payments to Review
- **Source:** New — entity: `autopay_transaction.ts` with `AutopayTransactionStatus.MANUAL_REVIEW`. Source type: system-detected.
- **Access:** `view_invoices, record_payment`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(*) FROM autopay_transaction WHERE status = 'MANUAL_REVIEW'`. Note: entity has no `is_active` column — filter on status alone.
- **Tap/filter:** AutoPay transactions filtered to manual review. No filter_code in the registry.
- **Icon:** `autopay_review.png`.
- **Sort/promotion:** T2, sort by `count`. Promotes To: —.
- **Dismissible:** Yes — midnight reset.
- **Feature gate:** Property must have autopay enabled.
- **Meaning as written:** AutoPay transactions that arrived but couldn't be auto-applied — needs manual matching.

</details>

---

## A11. Invoice didn't generate (Money)

**Registry status:** New.

**The situation:** This cycle's rent bills did not generate. Those tenants will not be billed until someone looks.

**What the card shows:** **{N} Invoice(s) Failed to Generate**. Subtitle: Tenants won't be billed until fixed — investigate now. Colour: red. Urgency: Revenue blocked. Button: Fix Now. Tap: the invoice generation log / failing tenants list. Cannot snooze. Hide if 0.

**Who sees it:** Anyone who can view invoices or add invoices on at least one property you have selected. Owner and admin always.

**How we count:** N is failed generation rows. No ₹ on the title. Several properties selected: add the numbers together.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Invoice Generation Failed
- **Source:** New — entity: `tenant_rent_generations.ts` (file name has plural, table name is singular). Source type: system-detected.
- **Access:** `view_invoices, add_invoices`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(*) FROM tenant_rent_generation WHERE is_generated = 0 AND due_date <= CURRENT_DATE`. Note: table name is singular `tenant_rent_generation`, not plural.
- **Tap/filter:** Invoice generation log / failing tenants list. No filter_code in the registry.
- **Icon:** `invoice_failed.png`.
- **Sort/promotion:** T1, sort by `count`. Promotes To: —.
- **Meaning as written:** Scheduled rent invoices that failed to generate.

</details>

---

## A12. Settlement still processing (Money)

**Registry status:** New.

**The situation:** Tenants paid online. The money is not in your bank yet. Usually 2-3 days. This card is for the ones that have already sat longer than that.

**What the card shows:** **{N} Settlement(s) Pending**. Subtitle: Online payments awaiting bank transfer. Colour: orange. Urgency: `{N} days in processing` (here N is days waiting, not how many settlements). Button: View Status. Tap: the settlement list, already filtered to pending. Can snooze. Comes back at midnight. Hide unless there is at least one pending **and** the oldest has sat more than 3 days. After 5 business days (Saturday and Sunday do not count), it jumps up the list. Snoozing does not move the money.

**Who sees it:** Anyone who can access bank details on at least one property you have selected. Owner and admin always.

**How we count:** N on the title is how many settlements are still processing. The urgency line is how many days the oldest one has waited. Several properties selected: add the counts together.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Online Settlements Pending; 5 days
- **Source:** New — entity: `settlement_scheduler` with status field. Source type: system-detected.
- **Access:** `bank_access`, ANY.
- **Aggregation:** `sum` on the card (count of pending settlements). `max` for the urgency line (days the oldest has waited).
- **Calc as written:** `COUNT(*) FROM settlement_scheduler WHERE status IN (0, 2) AND is_active = 1` — status 0=initiated, 2=attempted. Note: status 1=success (NOT in-progress), 3=failure.
- **Visibility as written:** Always shown when count > 0 and oldest pending > 3 days.
- **Tap/filter:** Settlement list filtered to pending. No filter_code in the registry.
- **Icon:** `settlement_pending.png`.
- **Sort/promotion:** T3, sort by `days_overdue`. Promotes To: T2 when any settlement pending > 5 business days. Ruled NEED-YOU #2 B, 16 Aug 2026. Calendar "5 days" is the leftover above.
- **Dismissible:** Yes — midnight reset.
- **Meaning as written:** Online payments collected but settlement to bank is still in processing.

</details>

---

## A13. Reimbursement due (Money)

**Registry status:** New.

**The situation:** Someone on your team is owed a reimbursement that is due. Pay Now on this card pays that. Salary and bonus are not this card.

**What the card shows:** **{N} Reimbursement(s) Due**. N is how many unpaid reimbursements. Subtitle: Pay reimbursements that are due. Colour: orange. Urgency: Overdue. Button: Pay Now. Tap: the team salaries screen. Can snooze. Comes back at midnight. Hide if 0. Snoozing does not pay anyone.

**Who sees it:** Anyone who can view the team on at least one property you have selected. Owner and admin always. They may only be able to see the team, not pay them. The button still says Pay Now. Shown for reimbursement due only, not salary or bonus.

**How we count:** We count reimbursement. N is unpaid reimbursement rows whose due date is today or earlier. Not salary. Not bonus. No ₹ on the title. Several properties selected: add the numbers together. The old formula also counted salary and bonus (engineering).

**Missing:** salary and bonus are not on this card until Pay Now works for them.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Staff Salary & Reimbursement Due; Staff Salary & Reimb. Due; Staff Payments Due; `{N} Staff Payment(s) Due` (four-name clash). Counted leftover `{N} Staff Payment(s) Due` is not the product title.
- **Source:** New — entity: `teamSalaries.ts` with `is_paid` (default 0), `due_date`, `type` fields. Source type: time-triggered.
- **Access:** `view_team`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(*) FROM team_salaries WHERE is_paid = 0 AND due_date <= CURRENT_DATE` — includes type: salary, bonus, reimbursement. Product counts reimbursement only (`type` = reimbursement).
- **Tap/filter:** Team salaries screen. No filter_code in the registry.
- **Icon:** `team_salary.png`.
- **Sort/promotion:** T2, sort by `days_overdue`. Promotes To: T1 when any salary > 7 days overdue (card field). Framework table: T1 when any salary > 7 days past due date. Promotion condition is a registry leftover; this card is reimbursement-only.
- **Dismissible:** Yes — midnight reset.
- **Cross-reference leftover:** Registry said A13 includes salary + bonus + reimbursement under Money, and E11 is salary under Daily Ops. That is not current product. Product lock: A13 is reimbursement only. E11 is the salary reminder ([E11](DAILY-OPS.md#e11-team-salary-due-daily-ops)).
- **Product (NEED-YOU #3 A+C, 16 Aug 2026):** A13 is reimbursement due only. E11 is the salary reminder, not a second Pay Now for reimbursement. Salary and bonus are not this card until Pay Now works for them. Not B.
- **Meaning leftover:** Registry meaning listed salaries, bonuses, or reimbursements unpaid. Product counts reimbursement only.

</details>
