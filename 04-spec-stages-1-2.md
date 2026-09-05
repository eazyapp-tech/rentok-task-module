---
title: "Task Module — Build Spec, Stages 1 and 2"
date: 2026-08-05
version: "1.1"
changelog: "1.1, 2026-09-05: reading table by role and the designer table added; decision labels carry their meaning inline; two wrong citations corrected (D44 at F37, D38 at F38); no requirement changed. 1.0, 2026-08-05: first version."
owner: "Sanchay"
status: "draft — for engineering review"
tags: [rentok, tasks, spec, stage-1, stage-2]
---

# Build Spec — Stages 1 and 2

What to build, how to know it is done, and what it does to the data.

**Scope is deliberately two stages.** [03-build-sequence.md](03-build-sequence.md) has seven. Stages 3–7 are not
specified here and should not be — stage 2 is the first thing a real manager touches, and what she does with
it will rewrite anything written for stage 3 today.

**This document does not estimate.** [05-engineering-asks.md](05-engineering-asks.md) asks for that.

## What is in here

The build spec for stages 1 and 2 only. Per item: what it is, what the code does today, numbered acceptance, data and API, edges. For engineering, QA and design; the by-role table below says which parts are yours. It is not a spec for stages 3 to 7, on purpose, and it does not estimate.

## Contents

- [How to read it](#how-to-read-it)
- [1. What the module does today](#1-what-the-module-does-today)
- [2. The question types (D84)](#2-the-question-types-d84)
- [3. Stage 1: Make what already exists safe](#3-stage-1--make-what-already-exists-safe)
- [4. Stage 2: Give the work an owner, a real cadence, and something worth filling in](#4-stage-2--give-the-work-an-owner-a-real-cadence-and-something-worth-filling-in)
- [5. Migration order](#5-migration-order)
- [6. Defects found while grounding](#6-defects-found-while-grounding)
- [7. What engineering decides](#7-what-engineering-decides)

## How to read it

Every requirement below has the same five parts:

- **What it is** — one line, from [02-requirements.md](02-requirements.md).
- **Today** — what the code actually does, with the file and line. Verified 5 Aug 2026 against `rentok-backend` at `master`.
- **Acceptance** — numbered, testable. QA writes scenarios from these.
- **Data / API** — columns, shapes, endpoints.
- **Edges** — the cases that will otherwise be found in production.

Decisions are cited as **D#**, with a few words of their meaning beside the label wherever the sentence does not already carry it. The full argument behind each lives in [CHANGELOG.md](CHANGELOG.md); its "Find a decision" table at the top lists every number in order. The words beside a label are a summary; if they and the CHANGELOG disagree, the CHANGELOG is right.

### How to read it, by role

*Added 2026-09-05.* Stage 1 adds no new capability and is invisible on purpose, but four of its items need screens: F44's save-as-a-copy flow, F38's archived filter and restore, F41's expired-link page and its name tap, and F26's empty state for a person with no tasks. Stage 2 is the first thing a manager or a staff member sees.

| You are | Read, per item | Skip |
|---|---|---|
| Engineering | All five parts | Nothing |
| Design | What it is · Acceptance · Edges, then [the designer's table](#what-a-designer-draws-in-stage-2) below | Today · Data / API |
| QA | Acceptance · Edges | Today · Data / API |
| Product, business | What it is, then [00-feature-map.md](00-feature-map.md) for the whole picture | The rest |

### What a designer draws in stage 2

*Added 2026-09-05. Every row is written from that item's own What it is, Acceptance and Edges lines further down; nothing here adds behaviour. Where the spec is silent, the cell says so.*

Two surfaces. **The manager's side** (scope, assignment, cadence, the one-off, category, the checklist builder, the library) is where she sets work up, on the manager web app. **The staff task page** (engineering calls it the runner) is a web page opened from a link on a cheap phone, often on 2G, where the work is done. Every row says which. Nothing in stage 2 is a new native app screen.

**F10 · Scope picker** · Manager's side · [spec section](#f10--scope-a-task-to-the-property-a-floor-specific-rooms-or-areas)
- Sees or does: The manager picks where a task applies: the whole property, one floor, a chosen set of rooms, or all rooms in one tap, with the same room picker a complaint uses.
- States and cases: Refused at save when the scope has no rooms. A warning on the schedule when a chosen room was later deleted. The floor choice is one floor today; whether the UI says so or accepts several is open, and Sanchay rules on it before design starts.

**F16 · Assignment mode** · Manager's side · [spec section](#f16--assign-to-several-people-two-ways-pooled-or-one-each)
- Sees or does: The manager chooses how several people share a task: pooled (any one finishes it for all) or one-each (everyone gets their own copy). No default is guessed.
- States and cases: A pooled task already done shows who did it, not a blank form. A second person submitting at the same moment is told who got there first. A warning at creation when the settings would create many tasks a day.

**M1 · Room cleaning as a task** · Manager's side; migration · [spec section](#m1--room-cleaning-becomes-an-ordinary-recurring-task-pooled-per-room)
- Sees or does: The room-cleaning shortcut button still works. Underneath it now creates an ordinary pooled task per room, so every cleaning records who cleaned.
- States and cases: A warning at migration for a property with cleaning on and no staff. The existing collective cleaning page keeps working or is replaced in the same release.

**P0 · Unassigned schedules** · Manager's side · [spec section](#p0--the-scheduler-skips-any-routine-with-nobody-assigned)
- Sees or does: Nothing new on its own. Removing the last person from a running schedule warns before it saves.
- States and cases: The "not running, nobody assigned" label on a schedule arrives in stage 7 with F48 (named in M1's edges), so until then the warning at save is the only signal.

**M6 · Checklist shape** · Neither; migration · [spec section](#m6--structure-gains-sections-and-branching)
- Sees or does: Nothing to draw. Sections and branching are drawn under F29 and F30.
- States and cases: None.

**F29 · Question types** · Both: builder and task page · [spec section](#f29--the-question-types-a-real-inspection-needs)
- Sees or does: Every question type, in the builder and on the task page. The twelve are listed under these blocks, plus branching.
- States and cases: An item hidden by an earlier answer is cleared, not submitted stale. An instruction cannot be marked required. A rating stores a number with its scale. The unit sits beside the number box. Voice notes are not in this stage.

**F30 · Per-item settings** · Both: builder and task page · [spec section](#f30--per-item-settings)
- Sees or does: Per-item settings in the builder: required, a photo required alongside any type, a reference picture beside the question, a note under the label, sections as collapsible headings.
- States and cases: Required blocks submission on the phone and again on the server. A "not applicable" choice is the way out of a required item the person genuinely cannot do, such as a locked room.

**F5 · Cadence picker** · Manager's side · [spec section](#f5--create-a-recurring-task-on-a-real-cadence)
- Sees or does: The cadence picker: daily, weekly, monthly, chosen weekdays (Mon, Wed, Fri), a chosen date each month.
- States and cases: The 29th, 30th and 31st in a short month fall on the month's last day, and the picker says so. Editing a cadence changes future runs only.

**F4 · One-off task** · Both: manager creates, task page shows it · [spec section](#f4--create-a-one-off-task-at-any-time)
- Sees or does: A one-off task created directly: who, due date and time, category, priority, description, and optionally a scope as in F10. It needs no checklist.
- States and cases: In the runner, a task with no checklist shows the description and a done action; that is a real state, not a broken one. It sits in the same list as scheduled work.

**F32 · Category, priority, description, end date** · Manager's side · [spec section](#f32--a-task-carries-a-category-a-priority-a-description-and-an-optional-end-date)
- Sees or does: On a one-off and on a schedule: a category (RentOk's built-in set plus the operator's own, with suggestions as she types), a priority from a small fixed set, a description, and for recurring work an optional end date.
- States and cases: Built-in categories cannot be renamed or removed. A property's own categories show first, then the rest of the account's.

**F9 · Checklist library** · Manager's side · [spec section](#f9--a-checklist-library-to-start-from)
- Sees or does: A library of starter checklists, filtered by property type and which modules are on. Copy one and edit it, or start blank.
- States and cases: The original stays untouched after a copy. A copy edits freely until it has open tasks (F44).

**F13 · Hindi and English starters** · Both · [spec section](#f13--starter-templates-ship-in-hindi-as-well-as-english)
- Sees or does: Starter checklists in Hindi and in English, following the language setting the property or user already has.
- States and cases: The app's own words (Submit, Overdue, Approve) stay English. Devanagari must render in the builder, the runner and any PDF.

**The twelve question types (F29), for the builder and the task page:** text · number · number with a unit · yes or no · one choice from a list · several choices · rating, 1 to 5 or 1 to 10 · date · time · one photo · several photos · an instruction that takes no answer. **Plus branching**, which is not a type but a setting on any item: it appears only after a named earlier answer.

---

## 1. What the module does today

Grounded, not remembered. Every row was read in the code on 5 Aug 2026.

| Thing | Where | State today |
|---|---|---|
| Template | `entities/taskTemplate.ts` | `structure` jsonb, a flat array of `{id, type, label, required, options?}`. Type union is **`text` · `number` · `yes_no` · `photo` · `select`** — five types. |
| Schedule | `entities/taskSchedule.ts` | `frequency` enum `one_time`/`daily`/`weekly`/`monthly`; `scope_type` string, default `property`; `send_time`; `next_run_at`; `is_active`. |
| Assignment | `entities/taskScheduleTeamMember.ts` | Assignees live on the **schedule**, not the task. |
| Task | `entities/taskInstance.ts` | `access_token` unique; `status` enum `pending`/`submitted`/`expired`; `responses` jsonb; `team_member_id` **nullable**; `latitude`/`longitude`; `reviewed_by`/`reviewed_at`/`rejection_reason`; `submitted_at`. |
| Scheduler | `services/taskScheduler.ts:109` | Resolves scope (`all_rooms`, `by_floor`, `manual`, else whole property), creates one task per entity, fans out one per assignee — **except room cleaning, which is gated out and creates one task with `team_member_id: undefined`** (`:152`, `:178`). |
| Trigger | `routes/taskRoutes.ts` | `POST /tasks/trigger` — **no `HeaderValidator`.** Open endpoint. |
| Runner | `routes/taskRoutes.ts` | `GET /tasks/runner/:token`, `POST /tasks/runner/:token/submit` — **no auth of any kind**, token only. |
| Submission | `controllers/taskController.ts:210` | Stores `responses` **unvalidated**; takes `team_member_id`, `latitude`, `longitude` **from the request body**; sets `submitted_at` server-side. |
| Template create | `controllers/taskController.ts:20` | Stores `structure` **unvalidated**. |
| Permissions | all admin routes | `HeaderValidator` only — authentication, no authorisation. Any authenticated user who supplies a `property_id` reads that property's tasks (`:166`). |
| Audit | — | None. |
| Expiry | — | `expired` exists in the status enum and **nothing ever sets it**. |

The wider picture of what the code does today (no event bus, the filter catalogue, the submit path) is in [reference/grounding-notes.md](reference/grounding-notes.md).

**Four defects found while grounding.** They are not requirements; they are bugs in shipped code, listed in
§6 so they are not lost. One of them — templates editable across accounts — should not wait for stage 1.

---

## 2. The question types (D84)

Settled now so F36 has a fixed target. Built in stage 2 (F29, the question types, and F30, per-item settings); validated from stage 1.

**Where the numbers come from.** Re-run against live data on **5 Aug 2026** — see M5 for the full table.

- **Confirmed:** 2,698 questions across 394 templates; 343 of them use `rating_5`, `rating_10` or `dropdown`. **There are no unknown type values beyond those three**, which is what makes M5's mapping complete.
- **Corrected:** the risk was recorded as *12.7% of checklists*. That was 343/2,698 — the share of **questions**. The share of **checklists** is **190/394 = 48.2%**.
- **Not re-run:** the counts behind the *new* types — 58 questions faking branching, 49 with a unit, 17 dates, 59 templates with 10+ questions. These come from the 4 Aug analysis in [the 4 Aug 2026 session handoff](history/2026-08-04-session-handoff.md). They argue *why* each type is worth building; none of them changes what gets built now that the list is committed (D84).

### The union

```ts
type QuestionType =
  // exists today
  | 'text' | 'number' | 'yes_no' | 'photo' | 'select'
  // added by D84
  | 'rating'            // 1–5 or 1–10, scale on the item
  | 'multi_select'      // several options, one answer array
  | 'photos'            // several photos on one item
  | 'date' | 'time'
  | 'number_with_unit'  // value + unit, unit set by the author
  | 'instruction'       // renders, takes no answer
```

**`select` is the only name.** Live data writes `dropdown`; the code calls the same thing `select`. M5
renames the data — we do not carry two names for one type.

**pass / fail / not-applicable is a `select` with three options**, not a type. A 1–5 rating is `rating` with
`scale: 5`, not five options — because insights (stage 6) needs to average it, and a select's options are
free text.

**`grid` stays dropped** (D84, the committed type list). Its answer is a table rather than a value.

### The item shape

`structure` stops being a flat array. Sections and branching cannot be expressed in one — this is **M6**.

```ts
interface Structure {
  version: 2
  sections: Section[]              // a checklist with no sections has exactly one, untitled
}

interface Section {
  id: string
  title?: string                   // absent = the implicit single section
  items: Item[]
}

interface Item {
  id: string                       // unique across the whole checklist, not just the section
  type: QuestionType
  label: string
  required: boolean                // D63: a required item blocks submission
  note?: string                    // author's instruction under the label
  reference_image_url?: string     // "it should look like this"
  photo_required?: boolean         // a photo alongside any other type (F30)
  options?: string[]               // select, multi_select
  scale?: 5 | 10                   // rating
  unit?: string                    // number_with_unit
  min?: number; max?: number       // number, number_with_unit, rating
  show_if?: { item_id: string; equals: string }   // branching
}
```

**Branching rules, so it cannot be made circular or unanswerable:**

1. `show_if.item_id` must refer to an item **earlier in document order**. Forward and self references are rejected.
2. The referenced item's type must be `yes_no` or `select`. Nothing else has a closed answer set.
3. `equals` must be one of that item's actual options (or `yes`/`no`).
4. **A hidden item is not required.** `required: true` on an item whose `show_if` did not match is satisfied by absence — otherwise every branch makes the form unsubmittable.

### Answers

```ts
responses: {
  [item_id: string]: {
    value: string | number | boolean | string[] | null
    unit?: string          // echoed back for number_with_unit, so history is readable
    photo_urls?: string[]  // photo, photos, or any item with photo_required
    skipped_reason?: 'hidden'   // the item's branch did not show
  }
}
```

---

## 3. Stage 1 — Make what already exists safe

Nothing new for the user. This stage fixes the module we already shipped. **It is invisible on purpose** —
which means it needs its own QA pass, because no manager will report a regression in something she cannot see.

**Order inside the stage:** M5 → F44 → F36. Everything else is independent.

---

### P1 — A reliable, authenticated scheduler

**What it is.** The recurring engine fires on a schedule nobody has to trigger by hand, and the endpoint that
fires it cannot be called by strangers.

**Today.** A scheduler runs — the product works — but it is registered nowhere in this repo. `POST
/tasks/trigger` is the only entry point and carries no `HeaderValidator` (`routes/taskRoutes.ts`). Someone or
something outside the codebase calls it. **The job is to find that caller and authenticate the endpoint, not
to build a scheduler.** Backend issue #6363.

**Acceptance.**
1. The caller is identified and written down — what it is, where it runs, who owns it.
2. `POST /tasks/trigger` rejects an unauthenticated request with 401.
3. The authenticated caller keeps working across a deploy, verified in staging before release.
4. A run that fires nothing is distinguishable in logs from a run that did not happen. Silence is not success.
5. If the identified caller turns out to be unowned or unreliable, it is replaced with a registered job in
   this repo — and that is a scope change engineering flags, not absorbs.

**Data / API.** Shared-secret header on `/tasks/trigger`, checked against an env var. Not `HeaderValidator` —
that expects a user, and this caller is a machine.

**Edges.**
- The caller may be firing per-property in a loop rather than `property_id: 'all'`. Authenticating one path and not the other silently halves the schedule.
- `updateNextRunAt` (`taskScheduler.ts:226`) resets a missed schedule to *tomorrow*, so a scheduler outage of two days produces no catch-up and no record that anything was missed. Confirm this is wanted before F45 starts calling things late.

---

### M5 — Rename the live question types

**Runs before F36.** Without it, F36 rejects **48.2% of production checklists** on the first morning.

**Today.** Counted against live data on 5 Aug 2026. **There are exactly six type values in production and no
unknown ones** — so M5's mapping is complete, which is the thing that had never been checked.

| Type in the data | Questions | Templates | In the code's union? |
|---|---|---|---|
| `yes_no` | 1,391 | 304 | yes |
| `text` | 777 | 199 | yes |
| `photo` | 187 | 150 | yes |
| `rating_5` | 173 | 136 | **no** |
| `dropdown` | 145 | 46 | **no** |
| `rating_10` | 25 | 21 | **no** |
| **Total** | **2,698** | **394** | |

**190 of 394 templates — 48.2% — contain at least one unknown type.** The earlier figure of 12.7% was the
share of *questions* (343 of 2,698) reported against the wrong denominator. **Nearly half of all checklists
break without M5, not one in eight.**

**Two things this also settled.** `select` and `number` have **zero** questions in production — `select`
because the data writes `dropdown` for the same thing, which makes the rename collision-free; `number`
because the box exists and nobody finds it (the reason F29 makes it findable).

**Acceptance.**
1. Every `structure[].type` in `task_template` is one of the committed types (D84) after the migration runs.
2. `rating_5` → `rating` with `scale: 5`. `rating_10` → `rating` with `scale: 10`. `dropdown` → `select`.
3. Any value still unmapped after the pass is **reported, not silently dropped** — a list of template ids goes to the migration output.
4. Re-running the migration changes nothing.
5. Submitted answers on renamed items still render in history.

**Data / API.** Migration in `src/migrations/`. Reads and writes `task_template.structure` only. No API change.

**Edges.**
- ~~Templates whose `structure` is null or not an array.~~ **Checked: there are none.** All 394 templates hold a JSON array. Keep the guard anyway — it costs a line — but it is not a case to design around.
- A template mid-edit while the migration runs. Take the write lock or run in a window.
- Re-count immediately before the migration ships. These figures are from 5 Aug 2026 and templates are created daily; the *shape* of the answer will hold, the row count will not.

---

### F44 — A checklist with open tasks cannot be edited

**Runs before F36**, because validation needs a checklist that cannot change under it.

**What it is.** Editing a live checklist is blocked; the operator saves it as a new one instead.

**Today.** `updateTaskTemplate` (`taskController.ts:715`) edits in place with no check for tasks in flight.

**Acceptance.**
1. `PUT /tasks/templates/:id` returns **409** when any `task_instance` referencing that template has `status = 'pending'`.
2. The 409 body names the count and gives the copy action — the operator is not left guessing.
3. `POST /tasks/templates/:id/copy` creates a new template with the same `structure`, a new id, and a name the operator sets. Schedules are not moved.
4. Renaming or changing the description is **allowed** while tasks are open. Only `structure` is blocked.
5. A template with no pending tasks edits exactly as it does today.

**Data / API.**
```
PUT  /tasks/templates/:id      → 409 { open_task_count } when structure changes and tasks are open
POST /tasks/templates/:id/copy → 201 { template }
```

**Edges.**
- A schedule pointing at the old template keeps pointing at it. Switching the schedule to the copy is a separate action the operator takes — do not do it silently, because in-flight work belongs to the old one.
- Expired tasks do not block. Only `pending`.

*(Engineering may prefer version-pinning each task to a template snapshot instead. That solves the same
problem and costs more; F44 records blocking as the cheaper fix and asks engineering to confirm.)*

---

### F36 — Every submission is validated on the server

**What it is.** A submission is checked against the checklist it belongs to before it is stored.

**Today.** `submitTask` (`taskController.ts:224`) assigns `instance.responses = responses` — whatever arrived.
Template creation is equally unchecked (`:33`).

**Acceptance.**

*On submit:*
1. Every key in `responses` matches an `item.id` in the template's `structure`. An unknown key is a **400**.
2. Every `required` item that was **shown** has a non-empty answer. A missing one is a 400 naming the item.
3. A required item that was hidden by `show_if` passes.
4. Each answer's shape matches its type: `number`/`number_with_unit` numeric and within `min`/`max`; `select` one of `options`; `multi_select` a subset of `options`; `rating` an integer within `scale`; `date`/`time` parseable; `yes_no` boolean; `instruction` **must not** carry an answer.
5. An item with `photo_required` has at least one `photo_urls` entry.
6. A 400 lists **every** failing item, not the first. A cleaner on 2G does not get five round trips.
7. A valid submission stores exactly what was sent and nothing more — no coercion, no defaults filled in.

*On template create and update:*
8. Every `type` is one of the committed types (D84). Unknown type is a 400.
9. `options` present and non-empty for `select` and `multi_select`; `scale` is 5 or 10 for `rating`; `unit` present for `number_with_unit`.
10. Item ids are unique across the whole checklist.
11. `show_if` passes the four branching rules in §2.

**Data / API.** One validator module, called from both `createTaskTemplate`/`updateTaskTemplate` (structure
validation) and `submitTask` (answer validation). It is the same shape knowledge in both directions — one
file, not two.

**Edges.**
- **A task created before its template was edited.** F44 makes this rare, not impossible (name edits still pass). Validate against the template as it is now, and if the task's answers reference item ids that no longer exist, reject with a message that says so rather than a generic 400.
- Photo urls are strings the client supplies. Validate that they are RentOk-hosted, or the "proof" is a link to anywhere.
- Empty string vs null vs absent. Pick one — **absent means unanswered**, empty string is an answer — and apply it in one place.

---

### F26 + M2 — Access control

**What it is.** Five permissions: see tasks · see only my own · create and assign (includes editing) ·
review · archive (D55, D56, D57). **Anyone without `view_team` / `add_team` / `edit_team` defaults to "see
only my own"; everyone else keeps today's access** (D69, correcting D13).

**Today.** No task route checks any permission. `getTaskInstances` (`taskController.ts:166`) returns a
property's tasks to any authenticated caller who names the property.

**Acceptance.**
1. Five permissions exist and are checked on every admin task route.
2. **Migration M2:** every existing user with `view_team`, `add_team` or `edit_team` keeps full task access. Everyone else lands on "see only my own".
3. A user with "see only my own" calling `GET /tasks/instances` receives only tasks where `team_member_id` is theirs — not a 403. The list is scoped, not refused.
4. A user with no task permission at all calling an admin route gets **403**, and the message does not reveal whether the resource exists.
5. Property scoping still applies underneath. A permission is not a cross-property key.
6. **M2 runs with M1** (D69). Tightening before room cleaning has assignees leaves every cleaner with nothing to open.
7. The owner is never locked out of their own account by the migration.

**Data / API.** Permissions on the existing team-member permission model — engineering confirms the storage
(D69's proxy is `view_team`/`add_team`/`edit_team`, already confirmed by Sanchay). Middleware, not
controller-body checks, so a new route cannot forget.

**Edges.**
- A team member with no `team_member_id` on any task sees an empty list. That is correct and must not read as an error state — it is the empty state, with words.
- The runner routes are **not** behind this. They are token-authenticated and stay that way (F41).

---

### F41 — The runner proves who is submitting; links expire

**What it is.** Identity comes from the task record, not the request body, and a link stops working when its
window closes (D54). **No login** — that friction would break the cold-load gate.

**Today.** `submitTask` takes `team_member_id` straight from `req.body` (`taskController.ts:229`). Anyone
holding a token can file work as anyone. Tokens never expire.

**Acceptance.**
1. On a task with a `team_member_id`, the submitter is that person. A `team_member_id` in the body is **ignored**, not trusted.
2. On a pooled task (no `team_member_id` — M1's cleaning case), the runner asks *who are you* and offers the schedule's assignees. The chosen one is written to the task on submit. This is why F41 depends on F16 (pooled assignment).
3. The name tap is a choice from a list, never free text.
4. A token past its expiry returns **410** on both `GET` and `POST`, with a message that says the link has closed and who to ask.
5. Expiry is set when the task is created, from the schedule's cadence: a daily task expires at the end of its day, weekly at the end of its week, monthly at the end of its month, one-off at its due date. **Server clock only** (F45, the server decides what is late).
6. An expired task moves to `status = 'expired'` — the enum value that exists today and is never set.
7. A submitted task cannot be reopened by its token.

**Data / API.** New column `task_instance.expires_at timestamptz`. `GET /tasks/runner/:token` returns the
assignee list when the task is pooled.

**Edges.**
- **Someone mid-submission when the window closes.** Accept the submission if it started before expiry, or the honest cleaner at 23:58 loses ten minutes of work. Decide with a grace window rather than a hard cut.
- Backfill: existing pending tasks have no `expires_at`. Set it from the schedule, and where that cannot be derived, leave null and treat null as *does not expire* — do not expire history retroactively.
- The token is in a WhatsApp link and will be forwarded. Expiry limits the damage; it does not remove it. Recorded, not solved.

---

### F37 — An edit log, and a lock after submission

**What it is.** Who changed what and when; a submitted record cannot be quietly altered. *(A citation to D44 was removed 2026-09-05: D44 rules version pinning, server-side lateness, collapsed reminders and offline one-offs, not an edit log. No decision covers the log itself; F37 stands as its own requirement.)*

**Today.** Nothing. `instance.save()` overwrites.

**Acceptance.**
1. Every change to a task, template or schedule writes a row: what, which record, who, when, before, after.
2. A task with `status = 'submitted'` rejects any change to `responses` with **409**.
3. The review path (`reviewed_by`, `rejection_reason`) is the **only** way a submitted task changes, and it is logged like everything else.
4. The log is append-only. No route deletes or edits a row.
5. The log is readable per record — a manager asking "who changed this" gets an answer without a database query.

**Data / API.** New table `task_audit_log`: `id`, `pg_id`, `record_type`, `record_id`, `action`, `actor_id`,
`actor_type` (`user` / `runner_token` / `system`), `before` jsonb, `after` jsonb, `created_at`. Indexed on
`(record_type, record_id, created_at)`.

**Edges.**
- The scheduler creates thousands of rows a day. Log **creation** at the run level (`run_id`, one row) rather than per task, or the log is bigger than the data.
- `before`/`after` on a 40-item `structure` is large. Store the changed keys, not the whole document.
- An actor with no user — the open trigger endpoint, the runner token — still needs an identity in the log. `actor_type` carries it.

---

### F38 — Archive and restore instead of deleting

**What it is.** Tasks, templates and rules archive; nothing hard-deletes. Archive is one of the five permission flags (D57). *(A citation to D38 was removed 2026-09-05: D38 is about a failed check raising its complaint, not about archiving.)*

**Today.** Less exposed than it sounds, and the one real hole is specific.

- `deactivateSchedule` (`taskController.ts:242`) is already a soft path — sets `is_active = false`, keeps history. **This is the pattern to follow.**
- `deleteSchedulePermanently` (`:324`) is guarded: it requires `?confirm=true` and refuses outright if any task instance exists. It can only ever delete a schedule that produced nothing.
- `deleteTaskTemplate` (`:662`) is guarded too — it refuses if any **submitted** instance exists, and refuses if any schedule is active. **But once past those guards it hard-deletes pending instances** (`:693`, `:703`), the schedules, and the template.

**So the gap is narrower than "everything hard-deletes":** submitted proof is already protected. What is
destroyed is **pending work** — tasks assigned to people and not yet done — and there is no archive or
restore concept anywhere.

**Acceptance.**
1. Delete routes archive. Nothing removes a row — in particular, **pending tasks are never destroyed** by deleting the template that created them.
2. An archived record disappears from every default list and stays reachable through an explicit archived filter.
3. Restore returns it to the active list.
4. Archiving a template does **not** archive its schedules; it blocks new schedules from using it and says so.
5. Archiving requires the archive permission (F26).
6. History against an archived template still renders.

**Data / API.** `archived_at timestamptz` + `archived_by` on `task_template`, `task_schedule`,
`task_instance`. Every existing list query gains `archived_at IS NULL`. **This is the migration's real
cost** — the filter must be added everywhere at once or archived records reappear in one forgotten list.

**Edges.**
- `deleteSchedulePermanently` is named what it does and its guard means it currently deletes almost nothing. Changing its behaviour under an unchanged name will confuse whoever calls it — rename the route.
- `deleteTaskTemplate`'s guards are on `submitted` and on active schedules, not on pending work. Archiving must close that specific path first; it is the only one that loses anything today.
- Restoring a schedule whose `next_run_at` is long past: recompute on restore rather than firing a backlog.

---

### F46 — Photo questions open the camera, never the gallery

**What it is.** Always, with no per-checklist setting (D52).

**Today.** Runner-side; not visible in the backend.

**Acceptance.**
1. A `photo` or `photos` item opens the camera directly. No gallery path.
2. The setting does not exist — there is nothing to configure and nothing to get wrong.
3. Where the platform cannot force it (desktop browser), the item says the photo must be taken now, and the submission is marked as taken outside the camera path rather than silently accepted as equal.
4. `capture` is set on the input, and it is verified on real Android — this is the browser attribute most often ignored.

**Edges.**
- A cleaner whose camera permission is denied has no path at all. Give a clear message naming the permission, not a dead button.
- iOS Safari behaves differently from Android Chrome. Both are tested, or the requirement is not met.

---

### F45 — The server decides what is late; missed reminders arrive as one message

**What it is.** Never the phone's clock (D44, lateness is computed by the server on sync; D23, every period is its own obligation).

**Today.** No due time exists on a task at all — only `next_run_at` on the schedule. Nothing is ever late.
`submitted_at` is already server-side (`taskController.ts:226`), which is the half that is right.

**Acceptance.**
1. A task carries its own due time, set when it is created. Lateness is computed from that and the server clock only.
2. **A due time is the end of the acceptable window, not the ideal moment** (D73). A night round due at 6am is not late at 2:01am.
3. A client-supplied timestamp is never read for anything.
4. A late submission is accepted and marked done-late. It is not refused.
5. Several missed reminders for one person collapse into one message with a count and one link.
6. Reminders are not sent outside daytime hours.

**Data / API.** New column `task_instance.due_at timestamptz`. Derived from the schedule's `send_time` and
cadence at creation.

**Edges.**
- Timezone. `task_schedule.next_run_at` is `timestamp without time zone` while `task_instance.submitted_at` is `timestamp with time zone` — comparing them will be wrong by 5h30m in exactly the way nobody notices until a 6am task is late at 12:30am. **Fix the mismatch before anything computes lateness.**
- Backfilled tasks with no `due_at`: never late. Null is not zero.
- F45's batching depends on F40 (notifications: four moments, all batched), which lands in stage 3. In stage 1 the server-side decision is what ships; the batched message follows.

---

## 4. Stage 2 — Give the work an owner, a real cadence, and something worth filling in

This is the first stage a manager can see. **M1 + P0 + M2 run together** (D69, the staff default must land when cleaning already has assignees; D78, routines are created unassigned, so the skip must not fire before cleaning has owners).

**Order inside the stage:** F10 → F16 → M1 → P0, then M6 → F29 → F30 → F9 → F13. F4, F5 and F32 are
independent.

---

### F10 — Scope a task to the property, a floor, specific rooms, or areas

**What it is.** Picked the way a complaint's location is picked, but multi-select with an "all rooms" option (D20).

**Today.** **More exists than the docs assume.** `taskScheduler.ts:118–139` already resolves `all_rooms`,
`by_floor` and `manual` (a `room_ids` array), defaulting to one task for the whole property. All three
exclude `unit_type: 'BED'`. **The engine is built; the operator has no way to reach it** — `createTaskSchedule`
(`taskController.ts:71`) never reads `scope_type` or `scope_value`, so every schedule created through the API
is property-wide.

**Acceptance.**
1. `POST /tasks/schedules` accepts `scope_type` and `scope_value` and stores them.
2. The four scopes work end to end: whole property · a floor · a chosen set of rooms · all rooms.
3. Scope is multi-select where it makes sense, with "all rooms" as one tap.
4. Rooms are picked with the same component a complaint's location uses. Not a second picker.
5. A scope that resolves to zero rooms is refused **at creation**, not silently at 6am — today `triggerTask` logs and returns an empty array (`:141`).
6. Changing a schedule's scope affects future runs only.

**Data / API.** No new columns — `scope_type` and `scope_value` exist. This is controller and UI work.

**Edges.**
- Beds are excluded from every scope. Confirm that is intended for tasks that are genuinely per-bed.
- A room deleted after the schedule was created: `manual` scope silently shrinks. Warn on the schedule, do not fail the run.
- `by_floor` reads `scope_value.floor` as a single value. A manager thinking "floors 1 and 2" gets one floor. Either accept an array or say so in the UI.

---

### F16 — Assign to several people two ways: pooled or one-each

**What it is.** Pooled means any one person completes it and it closes for all; one-each gives everyone their
own copy. Either way the system records who did what (D8, D19).

**Today.** **One-each already exists** — `taskScheduler.ts:152–167` creates one task per assignee per entity.
**Pooled does not exist.** The only thing resembling it is room cleaning, which is gated out of the fan-out
and produces a single ownerless task (`:178`) — pooled by accident, with no doer.

**Acceptance.**
1. A schedule carries a mode: `pooled` or `fan_out`. The creator picks; there is no default that guesses.
2. `fan_out` behaves as today — one task per assignee per entity.
3. `pooled` creates **one** task per entity with no `team_member_id`, visible to every assignee.
4. Completing a pooled task closes it for everyone, and records **who** completed it (via F41's name tap).
5. A second person opening an already-completed pooled task sees it is done and by whom — not a blank form.
6. A pooled task with one assignee behaves identically to a fan-out task with one assignee.

**Data / API.** New column `task_schedule.assignment_mode` enum `pooled` / `fan_out`, default `fan_out`
(today's behaviour for everything except cleaning). M1 sets cleaning schedules to `pooled`.

**Edges.**
- **Two people submitting the same pooled task at once.** First write wins; the second gets a 409 saying who got there first. Without this, one overwrites the other's proof.
- A pooled task's assignee list changes after the task is created. The task should honour the list as it was when created, or someone removed this morning can still file work.
- `all_rooms` × `fan_out` × 5 staff = 5 × room-count tasks per day. F49's reach preview exists for this reason and lands in stage 7 — until then, warn on creation.

---

### M1 — Room cleaning becomes an ordinary recurring task, pooled per room

**What it is.** Existing schedules carry over untouched; the shortcut button stays and creates a normal task
underneath (D60). **This is what gives the most common task in the building a doer.**

**Today.** `system_purpose === 'room_cleaning'` is special-cased out of fan-out (`taskScheduler.ts:152`) and
gets a collective link to `manager.rentok.com/rooms/cleaning-checklist` rather than a per-task runner link
(`:200`). No room has a recorded owner.

**Acceptance.**
1. Cleaning schedules become ordinary schedules: `scope_type = 'all_rooms'`, `assignment_mode = 'pooled'`.
2. The `system_purpose !== 'room_cleaning'` gate is **removed** from the scheduler. There is one path.
3. Existing cleaning schedules keep firing across the migration, at the same time, to the same people.
4. The cleaning shortcut button still works and now creates a normal schedule underneath.
5. Every cleaning task created after M1 records who cleaned the room.
6. The existing collective checklist page keeps working, or is replaced in the same release — not left pointing at a path that no longer produces its data.

**Data / API.** Migration: set `scope_type`, `assignment_mode` and assignees on existing
`system_purpose = 'room_cleaning'` schedules. Then delete the special case.

**Edges.**
- **The collective page reads `run_id`.** Removing the gate changes what that page finds. Check it before, not after.
- Properties where cleaning is enabled but no staff exist: after P0 those schedules stop firing. That is correct and must be visible in the UI (F48's "Not running — nobody assigned"), which lands in stage 7 — so until then it needs at least a warning at migration time.
- Existing pending cleaning tasks mid-flight when the migration runs. Let them finish on the old shape; apply the new one from the next run.

---

### P0 — The scheduler skips any routine with nobody assigned

**Runs with M1** and not before. Skipping first stops cleaning dead.

**Today.** The opposite: with no assignees, `triggerTask` creates one ownerless task (`taskScheduler.ts:178`).

**Acceptance.**
1. A schedule with no active assignee creates no tasks.
2. The skip is recorded — schedule id, when, why. Silence is how this gets found six weeks later.
3. Pooled schedules **do** create ownerless-by-design tasks and are not caught by the skip. The test is *no assignees on the schedule*, not *no `team_member_id` on the task*.
4. Removing the last assignee from an active schedule warns before it saves (D78).
5. Re-adding an assignee resumes the schedule with no further action.

**Edges.**
- An assignee who leaves the company. `taskScheduleTeamMember.is_active` already exists and is already filtered (`:112`) — confirm the leaver path sets it.
- A schedule that fires at 6am and loses its last assignee at 6:05am has already created the day's tasks. Those tasks stay; they have owners.

---

### M6 — `structure` gains sections and branching

**Runs before F29.** The shape is in §2.

**Acceptance.**
1. Every `task_template.structure` is migrated to `version: 2` — a flat array becomes one untitled section holding the same items in order.
2. Item ids do not change. Existing `responses` keep resolving.
3. Every reader of `structure` handles v2: the builder, the runner, the report, and F36's validator.
4. The migration is re-runnable and reports anything it could not convert.
5. A v1 document is never written again after the migration.

**Edges.**
- Anything outside this repo reading `structure` — the manager web app, an export, a report. Find them before the migration, not after.
- Run M6 **after** M5, so the type rename happens on the simpler shape.

---

### F29 — The question types a real inspection needs

**What it is.** The committed question types (D84), built.

**Acceptance.**
1. Every type in §2 renders in the builder, renders in the runner, stores an answer, and shows in history.
2. `rating` stores a number and carries its scale, so stage 6 can average it.
3. `instruction` takes no answer and cannot be marked required.
4. `photos` accepts several photos on one item, each through the camera (F46).
5. Branching hides and shows items live in the runner as earlier answers change, and a hidden item's answer is cleared rather than submitted stale.
6. `number_with_unit` shows the unit next to the box and stores it with the answer.
7. Every type validates per F36 — which is already written and needs no second pass, because D84 settled the union first.

**Edges.**
- **Answering, then branching away.** Item 5 shows only if item 2 is yes; the person answers 5, then changes 2 to no. Item 5's answer must be cleared, or the record contains an answer to a question that was not asked.
- A required item inside a hidden branch. §2 rule 4 settles it — hidden means not required.
- Voice note is listed in both F29 and F15b (voice notes as their own requirement). **Merge them** (recorded in the handoff, still open). It is not in stage 2's committed union above; it is V1.1 product intent.

---

### F30 — Per-item settings

**What it is.** Mark an item required (and a required item must be answered before submitting — D63), require
a photo on it, attach a reference picture, group items into sections, add a note.

**Acceptance.**
1. `required` blocks submission client-side and is enforced server-side by F36. Both, not either.
2. `photo_required` works alongside any type, not only photo items.
3. A reference image renders next to the question in the runner.
4. A note renders under the label and takes no answer.
5. Sections render as headings, collapse, and keep their order.
6. Every one of these is per item and set in the builder — none of them is a checklist-level setting.

**Edges.**
- A required item the person genuinely cannot do — the room is locked. With no "not applicable" path they will enter a false answer. `select` with a not-applicable option is the answer (D84), and starter templates should use it.

---

### F5 — Create a recurring task on a real cadence

**What it is.** Daily, weekly, monthly, chosen weekdays (Mon/Wed/Fri), and a chosen date each month (D16, configurable cadences).

**Today.** `frequency` enum is `one_time`/`daily`/`weekly`/`monthly` and `updateNextRunAt`
(`taskScheduler.ts:226`) adds 1 day / 7 days / 1 month. **Chosen weekdays and a chosen date each month do not
exist** — D16's "the engine already supports it" is true for the four it has and not for the two being added.

**Acceptance.**
1. All six cadences are creatable and editable.
2. Chosen weekdays fires only on those days.
3. A chosen date each month handles the 29th, 30th and 31st in short months by a stated rule — last day of the month, not skip.
4. Editing a cadence affects future runs only; work already created finishes and keeps its proof (D41, a rule's edits affect future tasks only).
5. `next_run_at` after any edit is correct without waiting for a run to fix it.

**Data / API.** Extend `frequency` and add `frequency_config` jsonb — `{weekdays: [1,3,5]}` or
`{day_of_month: 15}`. Do not encode it in `scope_value`.

**Edges.**
- `updateNextRunAt`'s catch-up resets a missed schedule to tomorrow and loses the miss (`:241–250`). With F45 computing lateness, a scheduler outage now silently erases the evidence that anything was missed. Decide the rule here, in stage 2, not in stage 3 when it becomes visible.
- Monthly on the 31st in February: state the rule in the UI, not only in the code.

---

### F4 — Create a one-off task at any time

**What it is.** Assign it, set a due date and time.

**Today.** Every task comes from a schedule. `frequency: 'one_time'` exists and deactivates the schedule
after firing (`taskScheduler.ts:236`), so a one-off is expressible but only through the schedule machinery,
and only for a template.

**Acceptance.**
1. A manager creates a task directly — assignee, due date and time, category, priority, description.
2. A one-off does not require a checklist. "Fix the gate light" is a task with no questions.
3. It appears in the same list as scheduled work (D5 — three sources, one list).
4. It carries a `due_at` and is subject to F45's lateness like anything else.
5. It can be scoped like F10, so "check rooms 1–5" is one action.
6. Creation is a callable action, not form-only (D10), so the assistant can reach it later.

**Data / API.** A `task_instance` with no `schedule_id`. **`schedule_id` is `NOT NULL` today** — it becomes
nullable, and every join that assumes a schedule needs checking. This is the structural cost of F4 and it is
easy to underestimate.

**Edges.**
- A one-off with no template: `template_id` is also `NOT NULL` today. Same fix.
- The runner renders from a template. With no template it renders the description and a done action. That is a real runner state, not a degraded one.

---

### F32 — A task carries a category, a priority, a description, and an optional end date

**What it is.** F20's one list (all three sources, filtered by category, sorted by due date then priority) has nothing to filter or sort otherwise.

**Today.** None of the four exists.

**Acceptance.**
1. Category, priority and description are settable on both a one-off (F4) and a schedule (F5), and inherited by tasks the schedule creates.
2. Categories come from the alerts' existing set, **and the operator can add her own** (M3).
3. A new category the operator types suggests previously-used ones as she types (the same guard D21 gives tags), or three managers create "Cleaning", "cleaning" and "Housekeeping" and the filter rots.
4. A recurring schedule takes an optional end date and stops firing after it.
5. Priority is a small fixed set, not free text.

**Data / API.** Columns on `task_schedule` and `task_instance`. **Categories belong to the account (D85)** —
the category table is keyed on `pg_id`, not `property_id`. RentOk's own categories (from the alerts, M3) sit
in the same list and are marked as built-in so they cannot be renamed or removed.

To keep the list usable, record which properties have used each category — a small join table
(`category_id`, `property_id`, `last_used_at`) is enough. A property's picker shows its own first, then the
rest of the account's. **Do not solve this by copying the category per property**, which is the thing D85
rejected.

---

### F9 — A checklist library to start from

**What it is.** RentOk recommends a starter set based on property type and which modules are on; she can copy
any template and edit its questions, or write her own (D58, D59).

**Today.** `getSampleTemplates` (`taskController.ts:61`) returns system templates. The endpoint exists; the
content and the recommendation do not.

**Acceptance.**
1. Starter templates exist as content, authored against the **full** D84 type set — this is why F9 sits in stage 2 and not stage 7.
2. The set shown is filtered by property type and which modules are on.
3. Copying a template creates an editable copy owned by the property. The original is untouched.
4. A copied template can be edited freely — F44 only blocks templates with open tasks.
5. An operator can start from a blank template.

**Edges.**
- Templates are authored once, against the final type list. Writing them on five types and rewriting later is the exact waste this sequencing avoids.
- Content is not engineering work and needs an owner named before the stage starts.
- **The shared library is editable by any account today** (§6 defect 3 — the `TEMPLATE_LIBRARY` exemption in `updateTaskTemplate`). F9 fills that library with the content this whole stage depends on. **Close the hole before the content lands**, not after.

---

### F13 — Starter templates ship in Hindi as well as English

**What it is.** D76, replacing "translate the app". The manager writes her own tasks and checklists in
whatever script she uses; that needs no engineering. **The starter templates are written twice.**

**Acceptance.**
1. Every starter template exists in Hindi and English.
2. The language shown follows the property's or the user's existing language setting — no new setting.
3. A copied template keeps the language it was copied from and is freely editable after.
4. The app's own words (Submit, Overdue, Approve) stay English (D76). This is accepted friction, not an omission.
5. Devanagari renders correctly in the builder, the runner, and any PDF or export.

**Edges.**
- Point 5 is the one that fails silently — font fallback in a generated PDF turns Hindi into boxes, and nobody notices until a manager sends one to an owner.
- This is content work on templates being authored anyway (D76), but it doubles the authoring, and that lands on whoever owns F9's content.

---

## 5. Migration order

Nothing here is optional and the order is not a preference.

| # | Migration | Runs | Because |
|---|---|---|---|
| **M5** | Rename `rating_5` / `rating_10` / `dropdown` | Stage 1, **before F36** | Otherwise **48.2%** of live checklists fail validation on day one (190 of 394, verified 5 Aug 2026) |
| **M2** | Permission defaults | Stage 1, **with M1** | Tightening before cleaning has assignees leaves cleaners with nothing (D69) |
| **M1** | Cleaning becomes an ordinary pooled task | Stage 2, **with M2, before P0** | Skipping unassigned routines first stops cleaning dead |
| **P0** | Skip routines with nobody assigned | Stage 2, **after M1** | Same |
| **M6** | `structure` v2 — sections and branching | Stage 2, **after M5, before F29** | The new types need the new shape |
| **M3** | Categories on system-raised tasks | Stage 6 (F57) | Out of scope here. Its per-property-or-per-account question is settled — account-wide (D85) — and F32 in stage 2 depends on that shape |

---

## 6. Defects found while grounding

Not requirements. Bugs in shipped code, found on 5 Aug 2026 while writing this. Recorded so they are not lost.

1. **A team member can be sent someone else's task link.** `taskScheduler.ts:201` falls back to
   `instances[0].access_token` when a member has no instance of their own. That token belongs to another
   person. Anyone following it files work as them — the exact hole F41 closes, reachable today.
2. **A member assigned to many rooms is sent one room's link.** `instanceByTeamMemberId`
   (`taskScheduler.ts:184–190`) keeps only the **first** instance per member. With `all_rooms` scope and 20
   rooms, the WhatsApp message links to room 1 and the other 19 are unreachable from the message.
3. **Any account can edit another account's templates, and everyone can edit the shared library.**
   `updateTaskTemplate` (`taskController.ts:726`) guards with
   `if (pg_id && template.pg_id !== pg_id && template.pg_id !== 'TEMPLATE_LIBRARY')`. Two holes in one line:
   **`pg_id` comes from the request body**, so omitting it skips the check entirely and any authenticated
   user can rewrite any template in any account; and the `TEMPLATE_LIBRARY` exemption means **every account
   can edit RentOk's shared starter templates** — the ones F9 is about to fill and F13 is about to translate.
   This is the most serious of the four and it is live now.
4. **Timezone mismatch across the task tables.** `task_schedule.next_run_at` and `created_at` are `timestamp
   without time zone`; `task_instance.submitted_at` and `created_at` are `timestamp with time zone`. Any
   comparison between them is out by 5h30m. **Fix before F45 computes lateness**, or a 6am task is late from
   half past midnight.

---

## 7. What engineering decides

Handed over with the spec, in addition to the three asks in
[05-engineering-asks.md](05-engineering-asks.md).

1. **F44 — block the edit, or version-pin each task to a template snapshot?** The spec assumes blocking as
   the cheaper fix. Version-pinning solves more and costs more.
2. **F4 — `schedule_id` and `template_id` become nullable on `task_instance`.** How much reads those columns
   assuming they are set?
3. **F41 — the grace window for a submission in flight at expiry.** A number, not a principle.
4. **M6 — who else reads `task_template.structure`** outside this repo?
5. **P1 — if the unidentified caller turns out to be unowned**, building a registered job is a scope change,
   not an absorption.
6. **§6 defect 3 — does the cross-account template edit need fixing now, ahead of stage 1?** It is live, it
   needs no design, and F9's content lands on top of it.
7. **F37 — audit log volume.** The spec proposes logging scheduler creation at the run level. Confirm that is
   enough for a dispute.

**Nothing here is product's.** F32's category question was the last one and is settled — **account-wide
(D85)**, reversing the per-property form of that decision from earlier the same day.
