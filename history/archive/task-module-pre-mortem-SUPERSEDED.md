---
title: "Pre-Mortem: Task Module Redesign (SUPERSEDED)"
date: 2026-07-20
owner: "Sanchay"
status: "superseded"
tags: [rentok, risk, pre-mortem, tasks, superseded]
---

> # ⛔ SUPERSEDED — DO NOT PLAN FROM THIS
>
> **Kept because several of its risks survive.** It was written against the old "write-backs" model, which is wrong: a task **reads** its linked thing's state and **suggests**; it never writes into another module.
>
> **What no longer applies:** T4 (a write-back corrupting rent/deposit/complaint) is largely moot — that mechanism is not being built. Every "write-back" framing in here is dead.
>
> **What still stands, and should carry into the replacement:** staff reading the tool as surveillance (T1), the runner failing on 2G/shared phones (T3), the access-control migration locking users out (T5), partial work lost on a shared device (T6), and an English-only launch (T9).
>
> **Also missing:** everything found in the 2026-07-21 product-lens review — see `../review-findings.md` (shared-device identity, notification storms, standing-rule integrity, lifecycle holes). The replacement pre-mortem must start from those.
>
> **Read instead:** `../CHANGELOG.md`, `../feature-requirements.md`, `../review-findings.md`.

# Pre-Mortem: Task Module Redesign

**Framing.** "Launch" = the redesign enabled on the first real property, with staff filling tasks through the runner and the write-backs live. There is no fixed calendar date; the risks below are written as gates — what must clear before launch — not invented dates.

> This module now writes into rent, deposits, and complaints, and it collects proof that a tenant dispute may later turn on. A failure here is not a slow week — it is a wrong payment record, a wrong deposit deduction, or a staff member who loses the proof that would have protected them. And the whole bet rests on staff trusting the tool. The bar is correctness and trust, not adoption speed.

Imagine it is launched and it failed. Three ways it most plausibly failed, worked backward below.

---

## Tigers (Real Risks)

### T1 — Staff read it as a watch-tool and quietly kill it (LAUNCH-BLOCKING for the bet)

The whole product rests on staff filling forms honestly because the record helps them. If the launch positioning, the manager's framing, or one wrong feature makes it read as the owner's surveillance, staff do the documented thing: they sabotage adoption — fill hollow forms, share the phone to dodge attribution, stop collecting real proof. Priya, the manager, is named in our own research (Persona Bible L178, L234) as a top-3 deal-blocker for exactly this reason.

- **Why it's a Tiger:** it is the bet's failure mode, and it fails silently — completion numbers can look fine while the proof underneath is hollow. That is why the PRD's counter-metric is "share of submissions with real proof," not raw completion.
- **Mitigation:** (1) the runner and staff record must show the staff member their own archive *first* — their defense, framed as theirs, before any owner view. This is F6 and it is not cosmetic. (2) No fines, no scorecard, no deduction — the Non-Goal holds this cycle and next. (3) Launch with the framing tested: the A/B on completion rate plus week-4 staff interviews (see Elephant E3) is the instrument that tells us the bet held. If it is not set up, we are flying blind on the one thing that matters most.
- **Test that must exist:** on the first property, week-4 staff interviews confirm staff describe the tool as protecting them, not watching them. If they describe it as the owner's eye, the positioning failed regardless of the completion number.

### T2 — The moat splits and only half ships, so we are just another checklist app (LAUNCH-BLOCKING)

The differentiator is two builds that are one outcome: F1 (finished tasks write back) this cycle, F2 (the business fires tasks on its own) next sprint. Two ways this collapses. First, F1 itself half-ships — the payment write-back lands but the complaint and deposit ones slip — and the module differentiates on nothing a manager can feel. Second, F2 gets deferred to "next sprint" and then to never, and the module can receive nothing automatically, so a manager still schedules everything by hand and the "right task appears on its own" promise never arrives.

- **Why it's a Tiger:** every rival (MaintainX, SafetyCulture, Xenia) is a competent checklist app. If we ship a competent checklist app with due dates, we spent a cycle to reach parity, not advantage. The write-backs are the only thing they structurally cannot copy.
- **Mitigation:** (1) F1 ships as a set, not a menu — the five write-backs are one requirement, and "some of them" is not a launch. (2) F2 gets a calendared sprint before this cycle ships, with an owner, not a "later." A deferral without a date is a cancellation. (3) The first property is chosen so at least the payment and complaint write-backs are exercised in its real daily work — a property where they never fire proves nothing.
- **Test that must exist:** on the first property, within two weeks, a real failed task raised a real complaint and a real collected payment marked a real invoice — end to end, on live data, not a demo.

### T3 — The runner is too slow or too fragile on the phones staff actually use (LAUNCH-BLOCKING — it is a ship gate)

The runner is the trust-critical surface and the least forgiving: shared cheap Android phones, one between several people, 2G signal. If it takes ten seconds to load or drops work when the signal dies, staff abandon it, and every downstream promise (proof, record, write-backs) collapses because the work never gets entered.

- **Why it's a Tiger:** it is the single biggest staff-side risk in the Audit, and "fast and offline-tolerant" is meaningless without numbers — which is why the PRD makes it a hard gate: cold-load under 3 seconds on 2G with no stored files.
- **Mitigation:** measure it, do not assert it. Trace the runner cold-load on a throttled 2G profile with an empty cache before launch; if it misses 3 seconds, Need 5 (proof of work) does not ship until it clears. Test on a real low-end device, not a fast phone with the network panel throttled.
- **Test that must exist:** a real cold-load trace on a 2G throttle, empty cache, under 3 seconds, on a device representative of the deployment — recorded, not estimated.

### T4 — A write-back corrupts the module it writes into (LAUNCH-BLOCKING — money and legal)

The write-backs touch rent, deposits, and complaints. The dangerous shortcut is a write-back that sets a "paid" flag or a deduction directly instead of going through the module's own front door. Marking rent collected must run through the same payment path a manual collection uses, or receipts, late-fine rules, and the money record silently drift wrong — and nobody notices until an owner reconciles and the numbers do not add up.

- **Why it's a Tiger:** this is a financial-integrity failure produced by correct-looking code. "Paid" is not a boolean in RentOk — it is a status plus a paid amount and date, only ever moved through the payment controllers. A task that patches the status directly is almost-right, which is the worst kind of wrong on money.
- **Mitigation:** every write-back routes through the owning module's real entry point (the PRD's first F1 rule), never a direct field set. The move-out deduction write-back reuses the existing inspection pipeline verbatim — it is already built and correct, so connect, do not rebuild. Golden-check the money paths: record a payment via a task and via a manual collection, and assert the invoice, receipt, and any late-fine come out identical.
- **Test that must exist:** a payment recorded through a task produces a byte-identical invoice/receipt/late-fine result to the same payment recorded manually. Any difference is a launch blocker.

### T5 — Access-control retrofit locks people out on release (LAUNCH-BLOCKING)

The module has no access control today — the runner submit route is open and the controller checks nothing. F9 builds it. The classic way this fails: the migration ships with the new permission flags defaulting to off, and on release day every manager and team member loses the tasks they could see yesterday. Adoption dies at the worst possible moment — the launch.

- **Why it's a Tiger:** a retrofit that tightens access is exactly where a default-off mistake silently removes capability from real users on day one.
- **Mitigation:** the migration defaults every existing user to the access they have today (the PRD's F9 rule). Nobody loses a task on release; access tightens deliberately, per property, later — never silently on the release. Test the migration against a copy of real property data and confirm the visible task set is unchanged before and after.
- **Test that must exist:** on a snapshot of a real property, the list of tasks each existing user can see is identical the moment before and the moment after the access-control migration runs.

### T6 — Partial work is lost on a shared phone (LAUNCH-BLOCKING — it is a ship gate)

The runner keeps partial work on the phone this cycle. Two ways it fails on the actual deployment: the network drops mid-task and the half-done work does not come back, or — the sharper one — three staff share one phone, and one person's saved-on-device draft surfaces under another person's session, attaching the wrong proof to the wrong person.

- **Why it's a Tiger:** "partial work survives a tab close, a network drop, and a phone restart" is a hard PRD gate. And the shared-device case is where on-device storage quietly does the wrong thing — see Elephant E1, which this Tiger depends on.
- **Mitigation:** on-device drafts are keyed to the person and the task run, not just the phone, so a shared device never leaks one person's draft into another's session. Test the three failure modes explicitly: mid-task network drop, tab close, phone restart — and the shared-device case on top.
- **Test that must exist:** start a task as person A on a shared phone, restart the phone, open as person B — B never sees A's partial work, and A's work is intact when A returns.

### T7 — Failed tasks flood the complaint queue or raise the wrong complaint (FAST-FOLLOW, possibly LAUNCH-BLOCKING)

F1a turns a complaint-worthy failure into a real complaint in the property's one complaint list. If the rule for "complaint-worthy" is too loose, every minor failed check becomes a complaint and the manager's complaint queue — a queue she relies on for real tenant issues — drowns. If it is too tight, real failures pass silently.

- **Why it's a Tiger:** a complaint is not free — it enters the escalation cron, sends WhatsApp reminders, and demands manager attention. A flood trains the manager to ignore the queue, which breaks the queue for its original job.
- **Mitigation:** the failure-to-complaint rule is explicit and narrow at launch — only clearly complaint-worthy failures raise one; the rest become a follow-up task, not a complaint. Watch the first property's complaint volume in week one and tighten before widening.
- **Test that must exist:** on the first property, the count of task-raised complaints in week one is a small, hand-checkable number, and each one is a real issue a manager agrees belongs in the complaint queue.

### T8 — The two open schema questions stall the build (LAUNCH-BLOCKING for timeline)

Two questions in the PRD are unanswered and both gate code: does the template library extend the existing task template or get its own store (Nimit), and which schema-change path this uses given there is no migration runner (Jatin). Neither is a product question — but until they are answered, F8 and F1 cannot start, and the cycle silently slips.

- **Why it's a Tiger:** they are quiet blockers. Nothing fails loudly; the build just cannot begin, and the slip is discovered late.
- **Mitigation:** both are filed as GitHub issues with named owners. They must be answered before their dependent builds start — F1 waits on the schema-path answer, F8 waits on the template-storage answer. Set the answer as the gate, not a calendar date.
- **Test that must exist:** both issues are closed with a decision before any code lands on F1 or F8.

### T9 — The runner ships English-only (LAUNCH-BLOCKING — it is a ship gate)

Priya reads Hindi more comfortably than English; Ramu needs Hindi or a regional language. If the runner ships English-first with translation as a fast-follow, the primary users cannot use it on day one, and adoption never starts.

- **Why it's a Tiger:** it is a hard launch gate in the PRD, and language is the kind of thing that slides to "v1.1" under deadline pressure — which, for this audience, is the same as not shipping.
- **Mitigation:** Hindi plus at least one regional language is in the launch scope, not the fast-follow list. The runner's text is built translatable from the first screen, not retrofitted.
- **Test that must exist:** a Hindi-first staff member completes a full task in the runner in Hindi, and a regional-language staff member does the same, before launch is called done.

---

## Paper Tigers (Overblown Concerns)

### P1 — "Managers won't adopt yet another task list"

The redesign does not add a new place — it puts tasks inside the daily list managers already open and already check (F7), reusing the existing pending-work surface's words and shape. The adoption cost of a brand-new surface is the thing being avoided by design. The real adoption risk is staff-side (T1), not manager-side.

### P2 — "There are too many kinds of task to build this cycle"

The task engine already supports the field types a checklist needs — text, number, yes/no, photo, choice. The gap the Audit found is not the kinds of task; it is the *connections* (write-backs, triggers, review, due dates). We are not building eleven task types; we are building the plumbing that makes the existing engine matter.

### P3 — "We need a native Task tab in the mobile app for this to feel real"

The web-view path is a deliberate, scoped decision, not a compromise — it ships faster and keeps the app the manager already knows. A native tab is a large mobile build for no user-visible gain this cycle. Treating its absence as a risk inverts the actual cost.

---

## Elephants (Unspoken Worries)

### E1 — Whose proof is it on a shared phone?

The deployment reality is one phone shared by several staff. The current runner link carries the task run and the property but no per-person identity. So when three people share a device, the system may not reliably know *which* of them did the work — and the whole bet is that the proof belongs to the person who collected it. If we cannot attribute reliably, the proof protects no one specifically.

- **Investigate before launch:** how is the individual staff member identified on a shared device? If the answer is "we assume one phone is one person," that assumption is false for this audience and needs a real answer (a per-person handoff on the device, a lightweight sign-in) before F6 can deliver its promise. T6 depends on this being solved.

### E2 — What happens to a staff member's record when they leave?

Staff churn is high — it is a named characteristic of this audience. The staff record (F6) is their proof archive. When they quit, what happens to it? The proof may be needed months later for a tenant dispute about work done while they were employed. Nobody has said whether the record survives the person, and a deposit dispute that turns on a move-out photo does not care that the staff member has moved on.

- **Investigate before launch:** decide retention — the proof attaches to the property and the task, and survives the staff member's departure. Confirm the export (F13) can reach a departed staff member's records.

### E3 — Is the A/B test that validates the bet actually built?

The bet — trust drives completion — is explicitly under test, not assumed. The PRD names an A/B on completion rate plus week-4 interviews. But a measurement plan named in a PRD is not a measurement plan that exists. If launch happens without the instrument, we will have opinions about whether the bet held, not evidence — and this is the one product decision we most need evidence on.

- **Investigate before launch:** confirm the completion-rate comparison and the week-4 interview script exist and have an owner before the first property goes live. Without it, T1's failure mode is invisible.

### E4 — Does "next sprint" for F2 have a real date and owner?

F2 (the event layer) is the second half of the moat, deferred to the sprint after this cycle. Deferrals of the hard, cross-cutting half of a build are exactly the ones that quietly become permanent. If F2 has no calendared sprint and no owner when this cycle ships, the honest expectation is that it never ships, and the module stays half a moat forever.

- **Investigate before launch:** F2 has a named owner and a scheduled sprint before this cycle closes — not a backlog entry, a calendared commitment. If it does not, say so plainly rather than carrying "next sprint" as a comfortable fiction.

---

## Action Plans for Launch-Blocking Tigers

| # | Risk | Mitigation | Owner | Gate (due) |
|---|---|---|---|---|
| T1 | Staff read it as surveillance and sabotage it | Staff-record-first framing (F6); no fines this cycle/next; A/B + week-4 interviews set up | Sanchay (product) + manager on first property | Before first property live; interviews at week 4 |
| T2 | Moat splits, only half ships | F1 ships as a set of five, not a menu; F2 gets a calendared sprint + owner | Sanchay + Nimit | F2 scheduled before this cycle ships |
| T3 | Runner too slow/fragile on 2G shared phones | Real 2G cold-load trace, empty cache, under 3s, on a low-end device | Jatin (build) + QA | Before Need 5 ships |
| T4 | Write-back corrupts rent/deposit/complaint | Every write-back through the owning module's front door; golden-check task vs manual payment | Jatin | Before F1 ships |
| T5 | Access retrofit locks users out on release | Migration defaults every existing user to today's access; test on real-data snapshot | Jatin | Before F9 ships |
| T6 | Partial work lost / leaks across shared-device users | On-device drafts keyed to person + task run; test all three failure modes + shared-device | Jatin + QA | Before Need 5 ships |
| T8 | Open schema questions stall the build | Close both GitHub issues with a decision before dependent builds start | Nimit (template store), Jatin (schema path) | Before F1 / F8 code starts |
| T9 | Runner ships English-only | Hindi + one regional language in launch scope; translatable from first screen | Mobile + build | Before launch called done |

**T7** (complaint flood) is a fast-follow watched from week one, escalated to launch-blocking only if the first property's task-raised complaint volume is not hand-checkable.

## Changelog

- **2026-07-20** — First pre-mortem for the Task module redesign, written against the Brief and PRD. Nine Tigers (eight launch-blocking, one fast-follow), three Paper Tigers, four Elephants. The four Elephants (shared-device identity, staff-record retention, the bet's measurement instrument, F2's real schedule) are the unspoken gaps most likely to be discovered too late.
