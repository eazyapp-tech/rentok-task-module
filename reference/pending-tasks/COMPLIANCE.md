# Compliance

These cards appear in View All under Compliance. Home may show at most one of them at a time in the stack.

Agreements, KYC, police verification.

**This is not the checklist / task / schedule Task module.**

This follows the sourced registry. Live-vs-code is in [GAPS.md](GAPS.md).

These cards are count only. No ₹ on the title. Compact ₹ only when How we count names an amount. None of these cards name one. Ruled [NEED-YOU #5](NEED-YOU.md) A, 16 Aug 2026.

---

## C1. Agreement renewals due (Compliance)

**Registry status:** Live on the old home screen and the new homepage.

**The situation:** Their agreement has already ended. You have not renewed it. Short-stay people are not on this card. Without a current agreement you have no paper if something goes wrong.

**What the card shows:** **{N} Agreement Renewal(s) Due**. N is how many staying people whose agreement has already ended. Subtitle: Avoid lapses — renew expired agreements. Colour: orange. Urgency: none. Button: Renew Now. Tap: the agreement renewal list. Can snooze. Comes back at midnight. Hide if 0. After 30 days past expiry it jumps up the list. Snoozing does not renew anyone.

**Who sees it:** Anyone who can view tenants on at least one property you have selected. Owner and admin always.

**How we count:** N is staying people who are not short-stay, with an agreement length on the person or the property, whose end date is already past. End date: latest renewal if there is one, else joining date plus the agreement length (11 months if none is set). Several properties selected: add the numbers together. A renewal already sitting unsigned is [renewed agreement not signed](#c2-renewed-agreement-not-signed-compliance). No agreement on file is [tenants to sign](#c3-tenants-to-sign-agreement-compliance). This card is expired, not unsigned.

**Missing:** Old home screen widget says this month. The count is already expired (end date before today), not this month.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Agreement Renewals Due; Agreement to renew this month
- **Source:** `service.ts:1611` (new system — `getPendingTasks()`, task `agreement_renewals_overdue`; SQL ~1426), `getAllTenants.ts:2698` (old system — `quickFilter()` widget "Agreement to renew this month", filter_code: 5005). Source type: time-triggered.
- **Access:** `view_tenants`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(tenant) WHERE status = 1 AND is_short_term IS NOT TRUE AND (agreement_period IS NOT NULL OR property.agreement_period IS NOT NULL)`. End date: **If** `last_agreement_renewal_date` is set → `COALESCE(latest tenant_agreement_renewals.agreement_end_date, last_agreement_renewal_date + COALESCE(tenant.agreement_period, property.agreement_period, 11) months - 1 day)`. **Else** → `date_of_joining + COALESCE(tenant.agreement_period, property.agreement_period, 11) months - 1 day`. Surfaces when computed end date < CURRENT_DATE.
- **Tap/filter:** Agreement renewal worklist. Old filter_code: 5005.
- **Icon:** `agreement.png`.
- **Sort/promotion:** T3, sort by `days_overdue`. Promotes To: T2 when any agreement is overdue by > 30 days past expiry.
- **Dismissible:** Yes — midnight reset.
- **Meaning as written:** Active tenants whose agreement end date has passed and needs renewal. Uses a multi-step date resolution that checks renewal history before falling back to joining date.
- **Status leftover:** Live (both systems).

</details>

---

## C2. Renewed agreement not signed (Compliance)

**Registry status:** Live on the old home screen. Not on the new homepage yet.

**The situation:** You already made a renewal on the system. They have not signed the new copy. An unsigned renewal is not a live agreement.

**What the card shows:** **{N} Renewed Agreement(s) Unsigned**. N is how many staying people with a renewal sitting unsigned. Subtitle: Renewals generated but not signed — no legal standing until signed. Colour: red. Urgency: none. Button: Get Signed. Tap: the tenant list, already filtered to unsigned renewals. Can snooze. Comes back at midnight. Hide if 0. Snoozing does not get a signature. Someone who can only view tenants still sees Get Signed.

**Who sees it:** Anyone who can view tenants on at least one property you have selected. Owner and admin always.

**How we count:** N is staying people who have a renewal record. Several properties selected: add the numbers together. Expired with no renewal yet is [agreement renewals due](#c1-agreement-renewals-due-compliance). No agreement on file is [tenants to sign](#c3-tenants-to-sign-agreement-compliance). A digital send still waiting is [e-sign pending](#c9-e-sign-agreement-pending-compliance). This card is a renewal sitting unsigned.

**Missing:** The formula counts a renewal record. It does not check whether they signed. The meaning says unsigned.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Renewed Agreement Not Signed; Renewed Agreement Unsigned; Pending Renewed Agreement not signed
- **Source:** `getAllTenants.ts:2747` (old system — `quickFilter()` widget "Pending Renewed Agreement not signed", filter_code: 8980). Source type: system-detected.
- **Access:** `view_tenants`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(DISTINCT tenant) WHERE status = 1 AND EXISTS(tenant_agreement_renewals record)`.
- **Tap/filter:** Tenant list filtered to unsigned renewals. Old filter_code: 8980.
- **Icon:** `rental_agreement.png`.
- **Sort/promotion:** T2, sort by `count`. Promotes To: —.
- **Dismissible:** Yes — midnight reset.
- **Meaning as written:** Tenants who have a renewal record in `tenant_agreement_renewals` but haven't signed the new agreement.
- **Status leftover:** Live (old system) — needs migration to new homepage.

</details>

---

## C3. Tenants to sign agreement (Compliance)

**Registry status:** Live on the old home screen. Not on the new homepage yet.

**The situation:** They are staying with you. There is no rental agreement on file. In a fight you have no paper.

**What the card shows:** **{N} Tenant(s) Without Agreement**. N is how many staying people with no agreement on file. Subtitle: No signed agreement on file — legal risk. Colour: red. Urgency: none. Button: Get Signed. Tap: the tenant list, already filtered to unsigned agreements. Can snooze. Comes back at midnight. Hide if 0. Snoozing does not get a signature. Someone who can only view tenants still sees Get Signed.

**Who sees it:** Anyone who can view tenants on at least one property you have selected. Owner and admin always.

**How we count:** N is distinct people staying with you whose agreement URL is empty. Several properties selected: add the numbers together. A booking with no agreement is [bookings to sign](#c4-bookings-to-sign-agreement-compliance). Two jobs. Do not merge. Expired that needs a renewal is [agreement renewals due](#c1-agreement-renewals-due-compliance). A renewal sitting unsigned is [renewed agreement not signed](#c2-renewed-agreement-not-signed-compliance).

<details>
<summary>For engineering</summary>

- **Registry also listed:** Tenants to Sign Agreement; Tenants Without Agreement
- **Source:** `getAllTenants.ts:2712` (old system — `quickFilter()` widget "Tenants to sign agreement", filter_code: 200). Source type: system-detected.
- **Access:** `view_tenants`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(DISTINCT tenant) WHERE status = 1 AND rental_agreement_url IS NULL`.
- **Tap/filter:** Tenant list filtered to unsigned agreements. Old filter_code: 200 (shared pointer with C4; C4 uses the booking widget `booking_qb`, `status = 2`).
- **Icon:** `rental_agreement.png`.
- **Sort/promotion:** T2, sort by `count`. Promotes To: —.
- **Dismissible:** Yes — midnight reset.
- **Cross-reference as written:** Same filter_code 200 as C4. C3 = staying (`status = 1`). C4 = booking (`status = 2`). Two jobs.
- **Meaning as written:** Active tenants who don't have a rental agreement uploaded.
- **Status leftover:** Live (old system) — needs migration to new homepage.

</details>

---

## C4. Bookings to sign agreement (Compliance)

**Registry status:** Live on the old home screen. Not on the new homepage yet.

**The situation:** They booked. There is no rental agreement on file. Get it signed before move-in day or day one is a scramble.

**What the card shows:** **{N} Booking(s) Without Agreement**. N is how many bookings with no agreement on file. Subtitle: Get agreements signed before move-in. Colour: orange. Urgency: none. Button: Get Signed. Tap: the booking list, already filtered to unsigned agreements. Can snooze. Comes back at midnight. Hide if 0. Snoozing does not get a signature. Someone who can only view tenants still sees Get Signed.

**Who sees it:** Anyone who can view tenants on at least one property you have selected. Owner and admin always.

**How we count:** N is distinct bookings whose agreement URL is empty. Several properties selected: add the numbers together. Someone already staying with no agreement is [tenants to sign](#c3-tenants-to-sign-agreement-compliance). Two jobs. Do not merge.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Bookings to Sign Agreement; Bookings Without Agreement
- **Source:** `getAllTenants.ts:2975` (old system — booking widget (`booking_qb`, `status = 2`) "Bookings to sign agreement", filter_code: 200). Source type: system-detected.
- **Access:** `view_tenants`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(DISTINCT tenant) WHERE status = 2 AND rental_agreement_url IS NULL`.
- **Tap/filter:** Booking list filtered to unsigned agreements. Old filter_code: 200 (shared pointer with C3; C3 uses staying tenants, `status = 1`).
- **Icon:** `rental_agreement.png`.
- **Sort/promotion:** T3, sort by `count`. Promotes To: —.
- **Dismissible:** Yes — midnight reset.
- **Cross-reference as written:** Same filter_code 200 as C3. Two jobs. Do not merge tenant vs booking.
- **Meaning as written:** Booked tenants who don't have a rental agreement uploaded.
- **Status leftover:** Live (old system) — needs migration to new homepage.

</details>

---

## C5. Tenants to complete KYC (Compliance)

**Registry status:** Live on the old home screen. Not on the new homepage yet.

**The situation:** They are staying with you. Their ID is not verified. Unverified people are a problem when police check.

**What the card shows:** **{N} Tenant(s) KYC Pending**. N is how many staying people whose ID is not verified. Subtitle: Complete ID verification for compliance. Colour: orange. Urgency: none. Button: Verify Now. Tap: the tenant list, already filtered to KYC pending. Can snooze. Comes back at midnight. Hide if 0. When a police verification deadline is within 7 days it jumps up the list. That jump uses a police deadline, not this KYC check. Snoozing does not verify anyone. Someone who can only view tenants still sees Verify Now.

**Who sees it:** Anyone who can view tenants on at least one property you have selected. Owner and admin always.

**How we count:** N is distinct people staying with you whose Aadhaar is not verified. Several properties selected: add the numbers together. A booking with KYC pending is [bookings to complete KYC](#c6-bookings-to-complete-kyc-compliance). Two jobs. Do not merge. Missing police paper is [police verifications](#c7-police-verifications-compliance). This card is ID, not the police document.

**Missing:** The jump names a police verification deadline. This card counts Aadhaar not verified. There is no deadline formula on this card.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Tenants to Complete KYC; Tenants KYC Pending
- **Source:** `getAllTenants.ts:2705` (old system — `quickFilter()` widget "Tenants to complete KYC Pending", filter_code: 201). Source type: system-detected.
- **Access:** `view_tenants`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(DISTINCT tenant) WHERE status = 1 AND is_aadhar_verified = false`.
- **Tap/filter:** Tenant list filtered to KYC pending. Old filter_code: 201 (shared pointer with C6; C6 uses the booking widget `booking_qb`, `status = 2`).
- **Icon:** `kyc.png`.
- **Sort/promotion:** T3, sort by `count`. Promotes To: T2 when police verification deadline within 7 days. Count is Aadhaar (`is_aadhar_verified = false`). C7 is the police-document card and does not promote.
- **Dismissible:** Yes — midnight reset.
- **Cross-reference as written:** Same filter_code 201 as C6. C5 = staying (`status = 1`). C6 = booking (`status = 2`). Two jobs. Police document is C7 (`police_verification_url IS NULL`).
- **Meaning as written:** Active tenants whose Aadhaar/ID has not been verified.
- **Status leftover:** Live (old system) — needs migration to new homepage.

</details>

---

## C6. Bookings to complete KYC (Compliance)

**Registry status:** Live on the old home screen. Not on the new homepage yet.

**The situation:** They booked. Their ID is not verified. Do it before move-in or you are scrambling later.

**What the card shows:** **{N} Booking(s) KYC Pending**. N is how many bookings whose ID is not verified. Subtitle: Verify IDs before move-in. Colour: orange. Urgency: none. Button: Verify Now. Tap: the booking list, already filtered to KYC pending. Can snooze. Comes back at midnight. Hide if 0. Snoozing does not verify anyone. Someone who can only view tenants still sees Verify Now.

**Who sees it:** Anyone who can view tenants on at least one property you have selected. Owner and admin always.

**How we count:** N is distinct bookings whose Aadhaar is not verified. Several properties selected: add the numbers together. Someone already staying with KYC pending is [tenants to complete KYC](#c5-tenants-to-complete-kyc-compliance). Two jobs. Do not merge.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Bookings to Complete KYC; Bookings KYC Pending
- **Source:** `getAllTenants.ts:2968` (old system — booking widget (`booking_qb`, `status = 2`) "Bookings to complete KYC", filter_code: 201). Source type: system-detected.
- **Access:** `view_tenants`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(DISTINCT tenant) WHERE status = 2 AND is_aadhar_verified = false`.
- **Tap/filter:** Booking list filtered to KYC pending. Old filter_code: 201 (shared pointer with C5; C5 uses staying tenants, `status = 1`).
- **Icon:** `kyc.png`.
- **Sort/promotion:** T3, sort by `count`. Promotes To: —.
- **Dismissible:** Yes — midnight reset.
- **Cross-reference as written:** Same filter_code 201 as C5. Two jobs. Do not merge tenant vs booking.
- **Meaning as written:** Booked tenants whose Aadhaar/ID hasn't been verified.
- **Status leftover:** Live (old system) — needs migration to new homepage.

</details>

---

## C7. Police verifications (Compliance)

**Registry status:** Live on the old home screen. Not on the new homepage yet.

**The situation:** They are staying with you. There is no police verification document on file. Many states require this. Missing it is a problem at inspection.

**What the card shows:** **{N} Police Verification(s) Pending**. N is how many staying people with no police paper on file. Subtitle: Legal requirement — submit before the next inspection. Colour: orange. Urgency: none. Button: Upload Now. Tap: the tenant list, already filtered to police verification pending. Can snooze. Comes back at midnight. Hide if 0. Snoozing does not upload a document. Someone who can only view tenants still sees Upload Now.

**Who sees it:** Anyone who can view tenants on at least one property you have selected. Owner and admin always.

**How we count:** N is distinct people staying with you whose police verification URL is empty. Several properties selected: add the numbers together. Unverified ID is [tenants to complete KYC](#c5-tenants-to-complete-kyc-compliance). Two jobs. This card is the police document, not Aadhaar.

<details>
<summary>For engineering</summary>

- **Registry also listed:** Police Verifications to Complete; Police Verifications
- **Source:** `getAllTenants.ts:2726` (old system — `quickFilter()` widget "Police verifications to complete", filter_code: 5009). Source type: system-detected.
- **Access:** `view_tenants`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(DISTINCT tenant) WHERE status = 1 AND police_verification_url IS NULL`.
- **Tap/filter:** Tenant list filtered to police verification pending. Old filter_code: 5009.
- **Icon:** `police.png`.
- **Sort/promotion:** T3, sort by `count`. Promotes To: —. C5 (not this card) promotes when a police verification deadline is within 7 days.
- **Dismissible:** Yes — midnight reset.
- **Cross-reference as written:** C5 counts `is_aadhar_verified = false`. C7 counts `police_verification_url IS NULL`. Two jobs.
- **Meaning as written:** Active tenants without a police verification document uploaded.
- **Status leftover:** Live (old system) — needs migration to new homepage.

</details>

---

## C8. KYC credits running low (Compliance)

**Registry status:** New.

**The situation:** KYC credits are running out. When they hit zero you cannot verify anyone. Recharge before a busy move-in week. Only if KYC is on.

**What the card shows:** **KYC Credits Running Low**. Presence only. No count and no ₹ on the title. Subtitle: Only {N} credits left — recharge to keep verifying. Colour: orange. Urgency: `{N} credits remaining`. Button: Recharge Now. Tap: the KYC credits recharge screen. Can snooze. Comes back at midnight. Hide unless remaining credits are under 10. When credits hit 0 it jumps up the list. Snoozing does not recharge.

**Who sees it:** Anyone who can view tenants on at least one property you have selected. Owner and admin always. Only if KYC is on.

**How we count:** Show if any selected property is under 10 remaining KYC credits. Remaining credits: allotted minus usage. The title is presence. Remaining credits sit on the subtitle and the urgency line. Count or presence only. No ₹. Several properties selected: show if any is low. Only from properties where KYC is on.

**Missing:** which property's remaining credits go on the subtitle when several are selected. Aggregation is any, not a sum of credits.

<details>
<summary>For engineering</summary>

- **Registry also listed:** KYC Credits Low; KYC Credits Running Low
- **Source:** New — not yet implemented. Entities: `instaveritas_credits`, `instaveritas_usage`. Source type: system-detected.
- **Access:** `view_tenants`, ANY.
- **Aggregation:** `any`.
- **Calc as written:** `SUM(instaveritas_credits.credits_alloted) - COUNT(instaveritas_usage) < 10` per property.
- **Tap/filter:** KYC credits recharge screen. No filter_code in the registry.
- **Icon:** `kyc_credits.png`.
- **Sort/promotion:** T3, sort by `count`. Promotes To: T2 when credits = 0.
- **Dismissible:** Yes — midnight reset.
- **Feature gate:** KYC verification enabled for property.
- **Meaning as written:** InstaVeritas verification credits are running low (below threshold).
- **Status leftover:** New.

</details>

---

## C9. E-sign agreement pending (Compliance)

**Registry status:** New.

**The situation:** You sent an agreement to sign digitally. They have not signed yet. Follow up. An unsigned send is not a live agreement. Only if e-sign is on.

**What the card shows:** **{N} E-Sign(s) Pending**. N is how many digital agreements still waiting. Subtitle: Digital agreements waiting for signature — follow up. Colour: orange. Urgency: none. Button: Follow Up. Tap: the e-sign agreements list, already filtered to pending. Can snooze. Comes back at midnight. Hide if 0. Snoozing does not get a signature. Someone who can only view tenants still sees Follow Up.

**Who sees it:** Anyone who can view tenants on at least one property you have selected. Owner and admin always. Only if e-sign is on.

**How we count:** N is pending e-sign rows (tenant or team member), still active, no signed time. Several properties selected: add only from properties where e-sign is on. No agreement on file is [tenants to sign](#c3-tenants-to-sign-agreement-compliance) or [bookings to sign](#c4-bookings-to-sign-agreement-compliance). A renewal sitting unsigned is [renewed agreement not signed](#c2-renewed-agreement-not-signed-compliance). Two jobs if the URL is still empty and the e-sign row is still pending. Do not merge.

<details>
<summary>For engineering</summary>

- **Registry also listed:** E-Sign Pending; E-Sign Agreement Pending
- **Source:** New — entity: `tenantAgreementState.ts` with `status` (default 'pending'), `party_type` (1=tenant, 2=team member), `signed_at` nullable. Source type: system-detected.
- **Access:** `view_tenants`, ANY.
- **Aggregation:** `sum`.
- **Calc as written:** `COUNT(*) FROM tenant_agreement_state WHERE status = 'pending' AND is_active = true AND signed_at IS NULL`.
- **Tap/filter:** E-sign agreements list filtered to pending. No filter_code in the registry.
- **Icon:** `esign.png`.
- **Sort/promotion:** T3, sort by `count`. Promotes To: —.
- **Dismissible:** Yes — midnight reset.
- **Feature gate:** E-sign feature enabled.
- **Meaning as written:** Digital agreements sent for e-signature but not yet signed by the tenant or team member.
- **Status leftover:** New.

</details>
