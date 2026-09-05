---
title: "Task Module — Product Vision Brief"
date: 2026-08-05
owner: "Sanchay"
status: "current"
companion: "Task Module - Feature Gap Audit.md"
tags:
  - rentok
  - brief
  - tasks
---

# Task Module — Product Vision Brief

> **What this is.** The bet behind the Task module redesign, in plain language. It says why we are doing this and what has to be true. [feature-requirements.md](feature-requirements.md) enumerates the features and [spec-stage-1-2.md](spec-stage-1-2.md) says how the first two stages get built; the [Feature Gap Audit](Task%20Module%20-%20Feature%20Gap%20Audit.md) holds the code-level evidence. This brief holds none of those — it holds the bet.

## What we are building

We are turning Task from a checklist app that does one job into the tool that tells a property's people what to do each day, proves it was done, and lets each level see and help the next — so no one waits to be told, and no one feels watched.

Start with the everyday problem. Most days the manager knows what needs doing and the staff wait to be told — even for work that is plainly their job. The manager spends the morning handing out the same instructions; the guard's rounds, the cleaner's routine, the meter reading happen only when someone says so. The module ends that. Each person's routine — the recurring work, RentOk's own recommendation of what a property like this should run, and their own reminders — shows up on their phone each day, so they get on with their job instead of waiting for it to be handed to them. And anyone can set a routine for themselves or for the people they lead: a manager a weekly collection review for herself, a founder a monthly health report for each of his managers. Because the work is now happening in the app, it proves itself — and each level can see where the standard is being met and step in where it is not.

Underneath, three things change. A task tied to a real thing in RentOk — a rent due, a tenant, a room — **shows that thing's live state**, so the person closing it can see what is actually true instead of checking another screen. An alert RentOk already raises — rent overdue, KYC missing — can be **turned into work**, one task per tenant, with an owner and a record. And a task that finds a problem carries it straight into a complaint, details already filled in, ready to raise. Nothing acts on its own — a person decides.

Today the module can run a scheduled checklist and little else. This redesign lifts it to the standard every serious operations tool holds.

## The problem

A property runs on more work than one person can hold in their head, and it is spread across several people. So the work fails in three ordinary ways.

Someone **forgets**. Someone **does it late**. Someone **says it is done** when it is not.

None of those needs a bad person. They are what happens when there are more jobs than one head can hold and nothing is keeping count.

**The expensive part is not the failure. It is that nobody finds out until it has turned into something else.**

The room is not cleaned on Monday. Nobody knows on Monday. The tenant complains on Thursday — and it arrives as *a complaint*, not as a missed cleaning. So the owner learns about a ten-minute failure through a slower, larger consequence, in a form that no longer says what caused it.

Across a year of our own data, **32% of complaints tied to a room are a repeat — the same room, the same kind of problem, within seven days.** For maintenance it is 33%. That is not a third of tenants finding new faults. It is a third of the complaint queue being somebody chasing something that was already reported and did not get done.[^complaints]

And there is a structural reason the owner learns late:

> **The person who reports on the work is the same person whose memory dropped it.**

Asking the manager for a better update does not fix that. The report has the same blind spot as the process. It gets worse the further you sit from the property — a manager on-site can at least walk the floor; a founder running eight properties only ever hears the problems that have already grown loud enough to reach him.

The Task module was built for one shape of this: a manager schedules a checklist, a staff member fills it in. That is where it stops. Today:

1. When a cleaning check fails, nothing happens — no complaint, no follow-up, no record that anyone noticed.
2. A task can be pinned to a real thing — a rent due, a tenant — but the module does nothing with that link. The task never shows whether the tenant has paid, so she checks another screen before she can close it.
3. Nothing the system already notices can be handed to a person as work. A room falls vacant and no one is told to make it show-ready; the home screen says five tenants owe rent and there is no way to send anyone to collect. The manager remembers, or it slips.
4. Tasks carry no due date, so nothing is ever late and nothing chases itself. There is no review — a manager cannot approve a task, reject it with a reason, or send it back.
5. The module has no access control of its own — the link staff submit through is open, and the controller checks no permissions.

So people keep the real system where it has always been: in their head, on WhatsApp, in a paper register. The module holds a copy of the checklist but not the truth of whether the work happened, and no one above the manager can see any of it.

**What we can honestly change.** The forgetting we can end outright — the work appears without anyone having to remember it, which is the one real prevention in here. Late work and claimed work we cannot prevent. What we can do is **collapse the distance between them happening and someone knowing** — from months to the same day. Nothing in this module forces a person to do anything.

## The market signal

Every competitor in this category — MaintainX is the clearest — sells the same thing: a way for an owner to watch whether staff did their jobs. They are watch-tools with friendly paint. The proof they collect is proof *against* the person who collected it. That sells, but it fights the person filling the form, and in a high-churn, low-trust staffing market that is a slow leak.

We are not trying to out-feature them, and we should not pretend to. They have built checklist tools for years and this cycle will not beat them on checklists.

**We are building a different thing for a different problem.** Their tool records the work. Ours moves the property's operating knowledge out of one person's head. In a business where staff change every month, that is the problem worth solving, and it is not what a watch-tool is built for.

Look at what a real operator is already reaching for. One of ours runs about fifty buildings. Their caretakers fill a daily checklist, their supervisors audit the caretakers weekly and audit the building monthly for permits and fire clearance, and they want a visit form with arrival and departure photos for anyone who goes to a site.

To run the inspection half of that they have **built and rolled out their own custom GPT** — it walks a caretaker through a room question by question and holds the report until every item is filled. A second one, for move-out asset documentation, is in design. They have **proposed a shared ChatGPT account across about fifty staff** simply to log when a water motor is switched on and off, because a failed motor costs them around ₹20,000 to repair.

An operator does not build their own AI tooling, or propose sharing one consumer account across fifty people, unless the need is large and nothing fits. That is the size of the gap.

## Who we are building for — the chain of accountability

The module serves a chain, and each link uses it to make the next one accountable and efficient — never by watching harder, always by making the work and its proof clear.

**The founder / multi-property owner.**[^owner] Runs several properties, rarely on any one. Today he trusts and hopes, and finds out about a problem when it is already loud. He sets the standard — the checklists a property like his should run — and sees which properties and managers are keeping to it. He holds his managers accountable for their property's health, across all of them at once, without being on-site.

**Priya — the on-site manager (team leader).**[^1] Runs the property day to day; assigns the cleaning, chases the KYC, handles the angry tenant at 4pm. Reads Hindi more comfortably than English. Her stated fear (Persona Bible L178, L234): that this becomes the owner's way of catching her out — and a manager who feels watched quietly kills adoption for everyone under her. She holds her staff accountable through clear ownership and a review loop, not by hovering.

**The housekeeping and maintenance staff.** Do the physical work. Change jobs often. Work on a cheap Android phone on a weak connection.[^devices] They are the people the proof is collected *from* — which is exactly why the proof has to protect them. Given clear tasks and their own record, they become accountable to the standard and efficient at meeting it, on their own.

**Ramu — the security guard.**[^2] Mans the gate, logs entries and exits, does the night rounds. His paper register is his job and his dignity. Position the app as replacing him and he resists; position it as the modern tool that makes his job respected and he adopts it.

## The root cause

The module was built as a checklist runner, and everything wrong with it follows from that one starting point. A checklist runner records answers. It does not use the link between a task and the real thing it is about. It cannot take a problem RentOk has already spotted and hand it to a person as work. It does not know that a failed answer should open a complaint, that a person needs permission to see a task, or that anyone above the manager can see any of it. All of it is missing not because it was cut, but because a checklist runner has no place to put it.

The fix is not more checklist features. It is teaching the module to use the link it already has, to turn what RentOk already notices into work someone owns, and to give everyone in the chain — staff, manager, founder — the clear view and the honest proof they each need.

## The mission: tell people what to do, prove it, help them improve

Underneath all three is one shift. Today a property runs on the manager's memory and daily instruction; the module makes it run on a system instead. The module does three things toward that, in the order a person meets them. They are one loop, not three tools — the same work, told, proved, and seen.

**It tells you what to do.** This is the everyday heart of it, and it is help, not oversight. People wait to be told — even for their own job — and managers burn their mornings telling them. The module gives each person their routine: the recurring work, RentOk's recommendation of what a property like this should run, and their own reminders. The work shows up; they do it; no one has to chase. That is daily efficiency that manages itself, and it runs in every direction — a founder sets a monthly routine for each manager, a manager a weekly one for herself, a staff member a reminder for a job they must not forget. The standards a property runs on get set once and then keep running, and they are applied **before** quality drops — the vacant room kept show-ready so it fills faster, the check done before the warranty lapses, the follow-up sent before the rent is badly overdue.

**It proves it was done** — with the proof owned by the person who did it. This is where accountability comes from, and it is not punishment. The photo and the timestamp are the worker's defense first and the record second. The proof is honest because the tool is built to protect the person giving it — and honest proof is the only kind worth holding anyone to.

**It lets each level see and help the next.** A founder sees which properties are keeping to the standard and holds his managers to it; a manager sees what is failing and where; a staff member sees their own record and gets better on their own. Each level enables and sees — it does not hover. A weak link surfaces before it becomes a fire, across every property, without anyone being on-site.

Efficiency is not a nice-to-have beside accountability — it is what makes the whole thing last. A tool that first helps you do your job, and saves your manager the morning spent assigning it, earns the right to also record it. A tool that only records gets abandoned by the second week.

## The bet: accountability without surveillance

**Proof that protects the person who collected it.**

Holding a team accountable and refusing to build a watch-tool are not in tension — one is the goal, the other is the only way to reach it. A tool built to catch and punish gets gamed: staff fill hollow forms, share phones to dodge blame, stop collecting real proof. You cannot hold anyone genuinely accountable on data they are motivated to fake. Fear makes people accountable to the watcher, not to the standard.

There is a sharper way to say why the record belongs to the worker. **Without one, honest work and claimed work look exactly the same** — which means the person who actually did it gets nothing for having done it, and the person who did not gets away with it. A record does not exist to catch the second person. It exists so the first one can prove they are not the second.

A tool the worker trusts gets used honestly, because it helps them — and honest data is the only thing real accountability can run on. When Priya cleans a room at 9am and the tenant complains at 4pm, the photo and the timestamp are *her* defense, not the owner's accusation. The owner sees the exception; the staff sees their record. Same data, but who it belongs to is the whole product. So a founder gets a **more** accountable team from a tool the team trusts than from one the team fears.

That is the bet, and it holds even where it costs us. **We will not ship a built-in fines system this cycle or next.** The moment staff believe the tool can dock their pay, they stop filling it honestly, and the proof collapses for everyone — including the owner who was paying for it. A watch-tool that no one fills is worth less than an honest tool that everyone does. Anti-surveillance is not a soft value bolted onto the mission; it is the mechanism that makes the mission work.

## What has to ship for the bet to hold

**The three jobs are the ship gate.** Tell people what to do — routines with real scope reaching the right person on their phone. Prove it happened — proof collected as part of the work, owned by the person who collected it. Let each level see — one list for the manager, the exceptions for the owner, and the manager seeing the same list about her own property. If any of those three does not work, the module has not done what we said it does.

The redesign also gives the module what every serious operations tool has and ours does not:

- **Proof collected as part of the work** — location, time, signature, photo, all owned by the staff member first. This is the headline, not a footnote.
- The staff member's own record of what they did.
- A review loop for the manager: approve, reject with a reason, send back.
- Due dates and reminders that chase late work.
- A comment thread on a task, with the same person-tagging used elsewhere in RentOk.
- Access control, finally built, so each level sees what it should.
- A first cut of insight — what is failing and where.
- A checklist template library, so a manager never starts from a blank box, with RentOk recommending what a property like hers should run.

The library is one deliverable inside this redesign, not a project beside it.

[feature-requirements.md](feature-requirements.md) lists every one of these as a numbered requirement, and [spec-stage-1-2.md](spec-stage-1-2.md) gives the first two stages their tests. This brief only claims the shape.

**Next sprint:** the property's own events making work appear without anyone looking — beyond the move-out that now creates the room-prep task — starts right after this one.

## What makes this last

One place for all of the property's work. Not the cleaning. All of it — the caretaker's daily round, the supervisor's audit, the monthly building check, the visit someone made on Tuesday, the one-off "get the water tank cleaned", the follow-up the manager owes herself. Every job, given to a named person, with proof, visible up the chain.

The shape underneath is always the same: **work goes to a person, they do it, there is a record, and each level above can see it.** That does not care what the job is, which role does it, or how often it happens — which is why there is no natural ceiling on what moves in.

And each thing that moves in is a piece of somebody's memory that the property no longer depends on. Priya builds her routines herself, so nobody can hand them to her replacement. That matters here more than almost anywhere: running a property on memory is fine when the same people are there next year, and **in a PG the people keep leaving and the property does not.** Today when a cleaner quits, Priya walks the new one around for days while things get missed. With the work already in the system, the new person opens the app and the job is there — every month, not once a year.

It also tells us what to build next, and the answer does not come from us. **The question is not "what feature now" but "what work is still outside the system"** — and the operator can answer that, because they are already doing that work somewhere else.

Three honest limits:

- **"Any work" is not true yet.** A visit with an arrival and a departure needs a small addition. Something logged four times a day is not a task at all and should not become one.
- **A routine carries the what and the when, not the how.** A checklist does not teach a new cleaner how this property wants a room cleaned — a reference picture on an item carries part of it, not all.
- **It is slow.** Nothing here protects anyone in week one. It only works once she has built enough routines to feel their absence.

So the template library is not a convenience. **Nothing accumulates until the work is in there** — and "you can put anything in it" is exactly how a tool ends up with nothing in it. The fastest path to a property that runs itself is a library she starts from, not a blank box.

For the same reason, a routine that gets switched off and forgotten is the thing to design against.

None of it happens if Priya does not trust the module. If it reads as the owner watching her, she never builds the routines, and there is nothing to accumulate. The no-fines, no-scorecard promise is not a value sitting beside this argument — it is what makes the argument possible.


## How the tasks show up — three sources, one place

The module carries three kinds of task, and puts them where people already look.

- **The system raises it** — "rent overdue," "KYC pending" — the alerts RentOk already surfaces from the data. These stay as they are.
- **A person assigns it** — the manager's scheduled and one-off work, including work she creates straight from an alert. This is the redesign.
- **A person keeps it for themselves** — a self-to-do or a log, with a reminder. "Collect keys from 204 tomorrow." Private, light, no approval.

For the manager, all three appear in the **same list she already checks, under the same categories** (Money, People, Compliance, Property, and the rest) — one place, whether the system raised the task or she did. The staff who do the physical work reach their own tasks through the runner, their working surface. Filling that manager list out to the full set of system alerts is a later phase, tracked separately so it does not weigh down this cycle.[^registry]

Making work appear on its own should feel like something managers already do — and it should start from what RentOk already knows. Today the home screen tells her five tenants owe rent and three need KYC. She can see the problem; she cannot hand it to anyone. So the redesign gives those alerts one action: **turn this into work.** She taps the alert, picks the rows she wants, assigns them, and gets one task per tenant — each with an owner, a due date, and a record of what was tried.

**We are not building rules that watch conditions on their own — not this cycle.**[^rules] Three of the routines a rule was meant to serve turned out to be events rather than conditions, one is already handled by complaint escalation, and we have no evidence yet for which conditions an operator actually wants. We will learn that by watching which routines managers keep re-scoping by hand.

## What we will not build this cycle

- **Fines, salary deductions, or a staff scorecard** — the bet forbids it. Staff who feel the tool can cost them money stop filling it honestly.
- **Anything acting on its own** — the module shows and pre-fills; a person always decides. No task closes itself, no complaint is raised without someone raising it, no alert becomes work without someone turning it into work.
- **Rules that watch a condition and create work on their own** — deferred until we have watched an operator want one.
- **A free-form rule builder** — an "if this, then that" editor is a developer tool, not an operator tool. Open-ended power comes through the assistant, later.
- **Attendance and shift-clocking** — a different product; folding it in blurs what this module is for.
- **Rebuilding move-out** — the move-out inspection is already a task that records deposit deductions correctly. We reuse it as the pattern; we do not touch it.
- **A native Task tab in the mobile app** — the runner and the manager's list reach the app through the existing web view, not a new tab.

Each is a thing someone will argue for. Each is out because the user does not need it this cycle — not because it is hard.

## What success looks like

**At launch, on the first real property:**
- A manager sets up tasks from the template library without a blank box, turns a rent-overdue alert into work for three named tenants, and the right work reaches the right staff on its own each morning.
- A task tied to a rent due shows whether it has been paid, so she closes it knowing, and a failed check hands the manager a ready-filled complaint — a person decides on both.
- A staff member on a weak connection completes a task with proof, loses the network mid-way, and does not lose the work — and can see their own record of what they did.
- The founder opens one view and sees which of his properties are keeping to the standard.

**Six months on:**
- Staff completion rate holds or rises — the honest test of whether the anti-surveillance bet is working.[^3]
- Managers run their properties from the module instead of their heads, and founders hold managers to a standard they can finally see.

## What this makes possible next

Once tasks know the real thing they are about, the module becomes the place a property's whole operating rhythm lives — and two horizons open.

The property starts to run its own standards without being told to. The move-out that now creates the room-prep task is the first of it; the rest of the property's events — a notice given, a warranty running out, a tenant joining — follow next cycle, so the work appears from what is actually happening. Rules that watch a condition are the step after that, once we know from real use which conditions matter.

And the biggest one — **you build any of it by talking.** RentOk's assistant, grounded in the real property, lets an operator say "clean all vacant rooms on the second floor every day, and if it's not done by 6pm, tell me" — and the work is drafted for her to confirm. This is also where rules that watch a condition eventually return: spoken, not configured. This is where the open-ended power lives without a developer tool ever appearing on screen, and it is the accessibility unlock for the exact people the bet is about: Priya and Ramu, who read Hindi better than English, can *speak* the work into existence. A generic task app's assistant can build "a task"; it cannot build "a task for all vacant rooms on the second floor," because it does not own the rooms. We do. To keep that door open, this cycle builds task creation as something the assistant can later call — not a screen it can never reach.

That is the horizon: a property that runs its own standards, holds its own people accountable through proof they trust, and can be operated by talking to it — the tool the staff want, not the tool the owner imposes.

---

[^owner]: The multi-property owner maps to RentOk's "Rajesh/Priya-owner" persona set; the on-site "Priya" in this brief is the *manager* persona, a distinct role. Named to keep the accountability chain clear, not to introduce new personas.

[^1]: Priya (on-site manager) is a composite persona from RentOk's Persona Bible (`icp_and_personas.md`). Surveillance fear sourced verbatim: line 178 — "If Priya sees RentOk as a surveillance tool that threatens her job, she will sabotage adoption. Must be positioned as 'your assistant that makes the owner trust you more.'" Line 234 names "Threatened Manager (Priya)" as a top-3 deal-blocker.

[^2]: Ramu (guard) is a composite persona from the same Persona Bible. Sourced verbatim: line 192 — "If the app replaces his paper register, he may feel threatened. Position as 'modern security tools that make your job respected.'"

[^complaints]: Live query against production, 4 Aug 2026. 70,513 room-linked complaints over 12 months, test properties excluded. A "repeat" is a complaint on the same room in the same category group as an earlier one. Within 7 days: 31.5% overall, 33.4% maintenance, 20.4% cleaning and housekeeping. Within 30 days it rises to 44.1%, which we treat as an upper bound because two genuinely different faults could fall in the same category. Cleaning and housekeeping together are 10.7% of all complaints. Categories come from the `first_level` field, which is free text and contains duplicate spellings; the grouping into cleaning / maintenance / other is ours.

[^rules]: **Reversed 2026-08-03 (D64).** Earlier versions of this brief sold standing rules — "clean every vacant room daily until it is filled" — as a headline capability and as ship-blocking. They are deferred entirely, along with the stored room-occupancy flag they needed. Vacant-room readiness is instead covered by a finished move-out creating the prep task directly (F3, pulled into this cycle), which needs no poller and no flag.

[^devices]: **Corrected 2026-08-03 (D74).** Earlier versions of this brief said staff "share a cheap Android phone, often one between several." That is not true of RentOk's customer base — staff have their own numbers. The **weak connection is real** and everything built for it stands: offline partial save, photo compression, the 3-second cold-load gate. Shared-device kiosk and quick-switch stay in the v2 backlog as a watch item, not a known gap. The same wrong claim is corrected in the Feature Gap Audit and in `review-findings.md`.

[^3]: Success measure is a launch A/B on staff completion rate plus qualitative interviews at week 4, comparing properties positioned "proof protects you" against a control. The claim that trust drives completion is the bet under test — measured, not assumed.

[^registry]: Building the manager's list out to the full set of system-detected tasks (the 65-entry pending-tasks registry) is a separate later phase — GitHub issue eazyapp-tech/rentok-backend#6249, which links the registry specification.

## Changelog

- **2026-08-05** — **Pointed at the docs that exist.** The brief referred twice to a PRD; no PRD is being written (one covering all 43 Band A+B requirements would be rewritten the moment stage 2 meets real managers). Those pointers now go to [feature-requirements.md](feature-requirements.md) and [spec-stage-1-2.md](spec-stage-1-2.md). **No change to the bet.** D84 (the question types) and D85 (categories belong to the account) landed the same day and are deliberately *not* here — both are build shape, which belongs in the spec.
- **2026-08-04 (f)** — **Handoff-review fixes.** The S2L evidence was overstated and is now restated to what the source supports: one inspection GPT is built and live, a second is in design, and the shared ChatGPT account for motor logging is proposed rather than running. "Google Forms" was invented by an earlier draft and is removed everywhere. Dropped the leftover "adds the things none of them have" claim, which contradicted the market section four lines below it. Cut the runs-on-memory argument from four places to one. Moved the ship-scope list back under "What has to ship", where it belongs. Limits made a list; bold thinned; the doc stopped calling itself one page.
- **2026-08-04 (e)** — **Put a number on the cost chain (D83).** 31% of room-linked complaints are a repeat on the same room within seven days — a third of the queue is someone chasing work that did not happen. The chain stops being a story. **Removed the vacancy half of it:** production data shows more than half of empty rooms sit over a month and 28% never refill within a year, so most vacancy is a demand problem this module does not touch.
- **2026-08-04 (d)** — **Rewrote the problem in the operator's words (D82).** The work fails three ways — forgotten, late, or said-done-when-it-was-not — and the expensive part is that nobody finds out until it has become a complaint or a vacancy. Named the structural reason: the person reporting on the work is the one whose memory dropped it. Added what we can honestly change (forgetting is prevented; the other two are surfaced sooner, not stopped) and removed any suggestion that the module blocks or forces work. Reframed the record as what lets an honest person prove they are honest, rather than what catches the dishonest one.
- **2026-08-04 (c)** — **Widened the story to the property's whole work (D81).** The previous version framed this around recurring routines, which was drawn from what the code does today rather than from the user. A real fifty-building operator runs six roles' worth of work and has built its own inspection GPT to cope — that evidence is now in the market section, stated only to what the source supports. Rewrote "What makes this last" around one place for all the work, named the underlying shape (work → person → proof → visible upward), and added the roadmap question that follows from it. Added the third honest limit: "any work" is not true yet.
- **2026-08-04 (b)** — **Named the moat (D80).** Removed "none of them can do the things we can" — it is not true on features and it invited a comparison that was never the argument. The moat is canonical sentence 8: a property runs on a system, not one person's memory, and the routines accumulate out of her head. Added "What makes this last", including the two honest limits (routines carry the what and when, not the how; the moat is slow) and the reason the no-fines promise is a precondition rather than a value beside it. Broke the differentiator paragraph into a list.
- **2026-08-04** — Reconciled with D64–D79. **Standing rules are out of the brief entirely** — they appeared in six places, including "what success looks like", describing a capability that was deferred. What replaces them: an alert RentOk already raises can be turned into work, one task per item. **Suggest-close is out too** — a task now *shows* the linked thing's live state and the person decides (D65). The "what has to ship" section was rewritten: the three jobs are the ship gate, and the entity link is what makes this ours, which is a different claim. The staff persona's shared-phone line was corrected (D74). Vacant-room readiness is back, via a finished move-out creating the prep task (F3).
- **2026-07-21 (d)** — Raised the mission's why one level: a property that ran on the manager's memory now runs on a system, so it does not break when a person is busy, away, or gone. Named the continuity payoff — new hires inherit the routine, the property survives the manager's leave, new properties start from recommended routines — as the answer to staff churn.
- **2026-07-21 (c)** — Rebalanced enablement-first. Named the everyday problem (people wait to be told, even for their own job; managers burn mornings assigning it) and made the module's first job "tell people what to do" — the routine runs itself, in every direction (self and reports), via recurring tasks, standing rules, recommendations, and reminders. Restructured the mission as three jobs — tell → prove → see — one loop, help not punishment. Accountability now sits as the trust layer of "prove," not the dominant theme.
- **2026-07-21 (b)** — Expanded to the full mission: the module makes every level of the business accountable and efficient to the level below (founder → manager → staff → self), and encodes standards that run themselves and maintain quality before it slips. Reconciled accountability with the anti-surveillance bet (honest data beats coerced data). Added standing rules framed as the "campaign" model operators already know, the three task sources in one place, and the assistant ("build by talking") as the horizon where open-ended power lives without a developer tool. Curated recommended rules this cycle; free-form builder explicitly out.
- **2026-07-21 (a)** — Corrected the core model: a task tied to a real thing reads its state and *suggests* closing (a person confirms); nothing acts on its own; a failed check hands a pre-filled complaint to the manager. Separated system-raised vs manager-created vs self-to-do tasks. Filed the 65-registry build-out as a later-phase issue (#6249).
- **2026-07-20** — First rewrite into the house vision-brief form; library reframed as one deliverable inside the redesign; code stripped to the companion Audit.
