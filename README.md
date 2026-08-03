# Task Module Redesign — Product Docs

The design docs for turning RentOk's Task module from a checklist runner into the tool a property's staff use to prove their work — where the proof protects the person who collected it.

**New here? Read in this order:**
1. [Task Module Brief](Task%20Module%20Brief.md) — the bet, in one page. Why we are doing this and what has to be true.
2. [Task Module PRD](Task%20Module%20PRD.md) — the requirements. What ships, as F1–F13.
3. [Task Module Pre-Mortem](Task%20Module%20Pre-Mortem.md) — what could go wrong, and the gates that must clear before launch.
4. [Feature Gap Audit](Task%20Module%20-%20Feature%20Gap%20Audit.md) — the engineer's evidence: 15 domains, ~250 capabilities, each scored against the code as it is today. Every "today it does / does not" claim in the other docs traces here.

These are design-only. No code ships from here.

## The scope, framed correctly

**The Task module redesign is the whole scope.** Everything below is one project: lifting the module to what every serious operations tool already has, plus the one thing none of them have.

**The checklist template library is one item inside this redesign** — the piece that solves setup, so a manager picks from ready templates instead of a blank box. It is feature requirement **F8** in the PRD. It has its own detailed documents (the library's own 00–08 specs and template content, in the `rentok-checklist-library` repo), but it ships as part of this redesign and is measured as part of it. It is not a parallel workstream.

## The bet

**Proof that protects the person who collected it.** Anti-surveillance framing, sourced verbatim from the Persona Bible (L178, L192, L234). Every accountability feature must land as protection, not control. No built-in fines — this cycle or next. The bet is under test at launch: an A/B on staff completion rate plus week-4 staff interviews.

## The moat — one outcome, two builds

The only capabilities a competitor structurally cannot copy, because only RentOk owns the property the work is about:

- **F1 — finished tasks write back into the business** (this cycle, ship-blocking). A collected payment marks the invoice paid; a completed KYC step marks the tenant verified; a move-out inspection records the deposit deduction; an asset check records its condition; a failed check raises a complaint. Every write-back goes through the owning module's own front door, never a shortcut.
- **F2 — the business fires tasks on its own** (next sprint, named and scheduled). Rent goes overdue and a collection task appears; a move-out completes and the deposit checklist appears.

Indivisible as an outcome; two builds as a plan. If either half slips, the module is just another checklist app — see Pre-Mortem T2.

## Ship gates (hard)

- The runner cold-loads under 3 seconds on a 2G connection with no stored files, and partial work survives a tab close, a network drop, and a phone restart — or the proof-of-work requirement does not ship.
- The runner ships in Hindi plus at least one regional language. Launch requirement, not a fast-follow.

## Open questions (gate the build, owners assigned)

Both are engineering-shape questions — the product need is settled in the PRD. Tracked as GitHub issues on the backend repo.

1. **Template storage (Nimit)** — does F8's library extend the existing task template or get its own store?
2. **Schema-change path (Jatin)** — which path the write-backs and new task states use, given there is no migration runner.

## Vocabulary (locked)

checklist · task · schedule · staff / manager / owner · beds. Never SOP, landlord, units, seamless, leverage. Indian-English is fine.

## Source of truth

These docs (`rentok-backend/docs/task-module/`) are the canonical home for the redesign. The `rentok-checklist-library` repo holds the F8 library sub-project's own detailed docs and template content. Vault mirror: `RentOk/PRDs/Task Module/`.
