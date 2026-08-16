# Rules (once)

Home-screen needs-attention feed (`getPendingTasks`). Not the checklist / task / schedule Task module.

**T1–T5, sort, and promotion exist so the stack knows which card is on top.** Home shows one card at a time. View All is grouped by category (Money, People, …), not one giant stack.

Cards say "see RULES" instead of repeating this. Everything here is what the [parent registry](sources/pending-tasks-registry.md) claims, except the rupee locks below. Those are product locks.

---

## Rupees on the card face

Compact ₹ (₹45K) on the card face — home stack and View All hub cards. Full amount after they tap. This is the locked format. The registry did not specify it. Ruled [NEED-YOU #4](NEED-YOU.md) B, 16 Aug 2026.

Compact ₹ only when How we count / calc names what the number is. Count or presence only until then. Do not put a fake ₹ on a money card. Ruled [NEED-YOU #5](NEED-YOU.md) A, 16 Aug 2026.

---

## Priority T1-T5

Each card has a **priority tier** (T1 through T5). That is how the home stack picks which card is on top. In View All, cards sit under their category, still ordered by tier inside that category. Higher-tier cards always sit above lower-tier cards.

| Tier | Everyday name | When the parent uses it | Typical colour |
|---|---|---|---|
| **T1** | Money at risk, or someone is blocked | Money is being lost now, or a person cannot act | Red |
| **T2** | A person is waiting | A tenant, a staff member, or a vendor needs a yes, a match, or a review | Red or orange |
| **T3** | Coming deadline | Not urgent today. Becomes T1 or T2 if ignored | Orange or blue |
| **T4** | Books and growth | Does not stop today's work. Adds up over time | Blue |
| **T5** | Nudge / periodic | No urgency. Monday food menu, watch a tutorial | Blue or green |

---

## Colour

| Colour | Hex (as claimed) | Meaning (as claimed) |
|---|---|---|
| Red | `#FF3C30` | Urgent. Blocks money, compliance, or the tenant lifecycle |
| Orange | `#EC9629` | Attention. Risks getting worse if ignored |
| Blue | `#1672EC` | Informational. Review when convenient |
| Green | `#30B502` | Positive. Something coming up to prepare for |

---

## Sort

On the home stack, and inside one View All category, cards sort in this order:

1. **Tier.** T1 first, T5 last.
2. **Sort signal** on that card, inside the same tier:
   - `amount`: highest ₹ first. Only when the card has a named ₹ formula. Ruled [NEED-YOU #5](NEED-YOU.md) A, 16 Aug 2026.
   - `count`: highest count first. Use this when the card has N and no named ₹.
   - `days_overdue`: most days past due first
   - `deadline`: closest deadline first
   - `static`: no live signal. Stable position in registry order. Presence cards with no amount use this or `count`, not `amount`.
3. **Tie.** Alphabetical by `task_id`, so the order does not flicker.

---

## Promotion (a card can jump a tier)

Some cards start in one tier and **promote** at runtime when the data gets worse. The parent names these:

| Card | Base | Promotes to | Condition as claimed |
|---|---|---|---|
| A12 Online settlement pending | T3 | T2 | Any settlement pending more than 5 business days (weekends do not count). Ruled [NEED-YOU #2](NEED-YOU.md) B, 16 Aug 2026. Calendar "5 days" is a registry leftover on [A12](MONEY.md#a12-settlement-still-processing-money) |
| A13 Staff pay overdue | T2 | T1 | Any salary more than 7 days past due date |
| B8 Stale bookings | T3 | T2 | Any booking older than 14 days (double the base threshold). Card: [B8](PEOPLE.md#b8-stale-bookings-people) |
| C1 Agreement renewal due | T3 | T2 | Any agreement overdue by more than 30 days past expiry |
| C5 KYC pending | T3 | T2 | Police verification deadline within 7 days for any unverified tenant |
| C8 KYC credits running low | T3 | T2 | Credits = 0 |
| D1 Complaints unassigned | T2 | T1 | Any unassigned complaint more than 24 hours old |
| D8 Escalated complaints L2 | T3 | T2 | Any L2 complaint approaching L3 threshold (72h) |
| D10 Overbooked rooms | T3 | T2 | An overbooked room has a new tenant move-in within 7 days |
| G2 WhatsApp balance low | T3 | T1 | Balance = 0. Card: [G2](PLATFORM.md#g2-whatsapp-balance-low-platform) |

Daily Ops E cards do not promote (parent —). Growth F cards do not promote (parent —). G1 does not promote (parent —). G3 is deprecated and does not show. People B8 is in [PEOPLE.md](PEOPLE.md#b8-stale-bookings-people). Compliance C1, C5, C8 are in [COMPLIANCE.md](COMPLIANCE.md). Property D1, D8, D10 are in [PROPERTY.md](PROPERTY.md). Daily Ops E11 is in [DAILY-OPS.md](DAILY-OPS.md#e11-team-salary-due-daily-ops). Growth F5 is in [GROWTH.md](GROWTH.md#f5-tenants-not-on-the-app-growth). Platform G2 is in [PLATFORM.md](PLATFORM.md#g2-whatsapp-balance-low-platform).

---

## Who sees what

Permissions are columns on `team_member_property`. Resolution as claimed:

1. **Owner** sees every card. No permission check.
2. **Admin** sees every card. Admin is treated as having every permission.
3. **Team member** sees a card if they have the required permission(s) on **at least one** of the filtered properties.
4. **Universal** cards (`Required Access: —`) are visible to everyone with app access.
5. **Feature flag** is separate. Even an Admin will not see a food card if that property does not have food on.

**ANY** (default): at least one of the listed permissions is enough. Example: `view_invoices, record_payment` with ANY means either is enough.

**ALL**: every listed permission. Rare. The parent says it is for compound capability. No Money card uses ALL.

Money default permission is `view_invoices`. Exceptions the parent names: refunds → `add_refund_access`; expenses → `view_expenses`; settlement → `bank_access`; salary → `view_team`.

---

## Assignee chip

"Assignee" is **not** a person you picked for this card. It is the set of team members who have the required permission on the filtered properties. Computed at read time from `team_member_property`. Not stored.

| Viewer | What they see |
|---|---|
| Owner / Admin | Chip under the button: `"Sanchay + 2 assigned"` or `"Unassigned"` |
| Other team members | No chip. Just the card |

| How many people have the permission | Chip |
|---|---|
| 0 | `"Unassigned"` (red chip) |
| 1 | `"Sanchay"` (the name) |
| 2 or more | `"Sanchay + {N-1} assigned"` (first name alphabetically), or `"You + {N-1}"` if the Admin looking is in the set |

Multi-property: count each person once. If Sanchay has `view_invoices` on Property A and Property B, that is 1 assignee, not 2.

Some cards use a different assignee source than `Required Access` (the parent example is complaints using `ComplaintResponderMap`). Those are per-card `Assignee Source` overrides. **No Money card has an override.**

---

## Multi-property

When the manager selects several properties (`pg_number_filter=1,2,3`), one card per task type, numbers combined.

- **Count:** add them. 2 move-out requests on A + 3 on C = 5.
- **Amount:** add them. ₹45K overdue on A + ₹80K on B = ₹1.25L. Card face uses this compact format; full amount after tap.
- **From N properties:** show `"from {N} properties"` when N > 1. Omit when N = 1.
- **Zeros do not count toward N.** If B has 0 complaints, only A and C count. N = 2, not 3.
- **Feature flags:** only add from properties where that feature is on.
- **Team members:** only add from properties where they have the required permission.

| Aggregation type | What it does | Used for |
|---|---|---|
| `sum` | Add counts (and amounts) from all in-scope properties | Most cards |
| `max` | Show the worst case | Urgency lines like "days overdue" |
| `any` | Show if any property matches. Count = 1 | Binary cards: app update, plan expiring, platform charges |

Tap to expand a per-property breakdown is **future, not v1**, as claimed.

Cards that say "Multi-property: add them, see RULES" mean `sum` unless the card names `any` or `max`.
