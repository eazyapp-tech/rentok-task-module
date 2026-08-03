---
title: "Task Module — Product Vision Brief"
date: 2026-07-21
owner: "Sanchay"
status: "draft"
companion: "Task Module - Feature Gap Audit.md"
tags:
  - rentok
  - brief
  - tasks
---

# Task Module — Product Vision Brief

> **What this is.** The one-page bet for the Task module redesign, in plain language, before the PRD. It says why we are doing this and what has to be true. The PRD enumerates the features; the [Feature Gap Audit](Task%20Module%20-%20Feature%20Gap%20Audit.md) holds the code-level evidence. This brief holds neither — it holds the bet.

## What we are building

We are turning Task from a checklist app that does one job into the tool that tells a property's people what to do each day, proves it was done, and lets each level see and help the next — so no one waits to be told, and no one feels watched.

Start with the everyday problem. Most days the manager knows what needs doing and the staff wait to be told — even for work that is plainly their job. The manager spends the morning handing out the same instructions; the guard's rounds, the cleaner's routine, the meter reading happen only when someone says so. The module ends that. Each person's routine — the recurring work, the rules that fit the property's state, RentOk's own recommendation of what a property like this should run, and their own reminders — shows up on their phone each day, so they get on with their job instead of waiting for it to be handed to them. And anyone can set a routine for themselves or for the people they lead: a manager a weekly collection review for herself, a founder a monthly health report for each of his managers. Because the work is now happening in the app, it proves itself — and each level can see where the standard is being met and step in where it is not.

Underneath, three things change. A task tied to a real thing in RentOk — a rent due, a tenant, a room — knows the real thing it is about and **suggests** what to do next; the person decides. A manager can set a standing rule once — "clean every vacant room daily until it is filled" — and the work appears on its own, for whoever matches, until they stop matching. And a task that finds a problem carries it straight into a complaint, details already filled in, ready to raise. Nothing acts on its own — the system suggests, the person confirms.

Today the module can run a scheduled checklist and little else. This redesign lifts it to the standard every serious operations tool holds — and then adds the things none of them have, because only RentOk owns the rent, the tenants, and the standards the work is about.

## The problem

A property runs on work that has to happen every day and has to be provable: rooms cleaned, meters read, KYC collected, move-out inspections done, deposits settled, complaints closed. Two problems sit on top of each other. The first is that the work often does not happen until someone is told to do it — staff wait for instruction even on their own routine, and the manager spends every morning giving it. The second is that even when it happens, no one can be sure it did — who did it, when, and what to do when it did not. And both get worse the further you sit from the property: a manager on-site can at least walk the floor and hand out the work; a founder running eight properties is flying blind on all but the loudest problems.

The Task module was built for one shape of this: a manager schedules a checklist, a staff member fills it in. That is where it stops. Today:

1. When a cleaning check fails, nothing happens — no complaint, no follow-up, no record that anyone noticed.
2. A task can be pinned to a real thing — a rent due, a tenant — but the module does nothing with that link. The due gets paid and the task still sits open, waiting for someone to notice and close it by hand, even though the system already knows.
3. There is no way to say "keep doing this while the condition holds." A room falls vacant and no one is reminded to keep it clean and show-ready until it fills. The manager remembers, or it slips.
4. Tasks carry no due date, so nothing is ever late and nothing chases itself. There is no review — a manager cannot approve a task, reject it with a reason, or send it back.
5. The module has no access control of its own — the link staff submit through is open, and the controller checks no permissions.

So people keep the real system where it has always been: in their head, on WhatsApp, in a paper register. The module holds a copy of the checklist but not the truth of whether the work happened, and no one above the manager can see any of it.

## The market signal

Every competitor in this category — MaintainX is the clearest — sells the same thing: a way for an owner to watch whether staff did their jobs. They are watch-tools with friendly paint. The proof they collect is proof *against* the person who collected it. That sells, but it fights the person filling the form, and in a high-churn, low-trust staffing market that is a slow leak.

None of them can do the things we can. They do not own the rent due, the tenant record, or the complaint queue, so their tasks cannot know the real thing they are about. They do not know how a property like yours operates, so they cannot suggest the rules you should run. And they cannot hold a real chain of people accountable, because a watch-tool only ever points down at the worker. We can do all of it, because we already run the property the work is about.

## Who we are building for — the chain of accountability

The module serves a chain, and each link uses it to make the next one accountable and efficient — never by watching harder, always by making the work and its proof clear.

**The founder / multi-property owner.**[^owner] Runs several properties, rarely on any one. Today he trusts and hopes, and finds out about a problem when it is already loud. He sets the standard — the checklists, the rules a property like his should run — and sees which properties and managers are keeping to it. He holds his managers accountable for their property's health, across all of them at once, without being on-site.

**Priya — the on-site manager (team leader).**[^1] Runs the property day to day; assigns the cleaning, chases the KYC, handles the angry tenant at 4pm. Reads Hindi more comfortably than English. Her stated fear (Persona Bible L178, L234): that this becomes the owner's way of catching her out — and a manager who feels watched quietly kills adoption for everyone under her. She holds her staff accountable through clear ownership and a review loop, not by hovering.

**The housekeeping and maintenance staff.** Do the physical work. Change jobs often. Share a cheap Android phone, often one between several, on a weak connection. They are the people the proof is collected *from* — which is exactly why the proof has to protect them. Given clear tasks and their own record, they become accountable to the standard and efficient at meeting it, on their own.

**Ramu — the security guard.**[^2] Mans the gate, logs entries and exits, does the night rounds. His paper register is his job and his dignity. Position the app as replacing him and he resists; position it as the modern tool that makes his job respected and he adopts it.

## The root cause

The module was built as a checklist runner, and everything wrong with it follows from that one starting point. A checklist runner records answers. It does not use the link between a task and the real thing it is about. It has no way to hold a standing rule — "while this is true, keep doing that." It does not know that a failed answer should open a complaint, that a person needs permission to see a task, or that anyone above the manager can see any of it. All of it is missing not because it was cut, but because a checklist runner has no place to put it.

The fix is not more checklist features. It is teaching the module to use the link it already has, to run standing rules against the property's real state, and to give everyone in the chain — staff, manager, founder — the clear view and the honest proof they each need.

## The mission: tell people what to do, prove it, help them improve

Underneath all three is one shift. Today a property runs on the manager's memory and daily instruction; the module makes it run on a system instead. The routines, the standards, and the record stop living in one person's head — so the property does not break when that person is busy, on leave, or gone. The module does three things toward that, in the order a person meets them. They are one loop, not three tools — the same work, told, proved, and seen.

**It tells you what to do.** This is the everyday heart of it, and it is help, not oversight. People wait to be told — even for their own job — and managers burn their mornings telling them. The module gives each person their routine: the recurring work, the rules that fit the property's state, RentOk's recommendation of what a property like this should run, and their own reminders. The work shows up; they do it; no one has to chase. That is daily efficiency that manages itself, and it runs in every direction — a founder sets a monthly routine for each manager, a manager a weekly one for herself, a staff member a reminder for a job they must not forget. The standards a property runs on get set once and then keep themselves: automated by the rules, and applied **before** quality drops — the vacant room kept show-ready so it fills faster, the check done before the warranty lapses, the follow-up sent before the rent is badly overdue.

**It proves it was done** — with the proof owned by the person who did it. This is where accountability comes from, and it is not punishment. The photo and the timestamp are the worker's defense first and the record second. The proof is honest because the tool is built to protect the person giving it — and honest proof is the only kind worth holding anyone to.

**It lets each level see and help the next.** A founder sees which properties are keeping to the standard and holds his managers to it; a manager sees what is failing and where; a staff member sees their own record and gets better on their own. Each level enables and sees — it does not hover. A weak link surfaces before it becomes a fire, across every property, without anyone being on-site.

And because the operation now lives in the system, it survives the people changing — which, in a business where staff quit constantly, is the whole game. A new hire opens the app and inherits the routine instead of shadowing someone for a week. The property keeps running when the manager is on leave. A new property starts from the routines RentOk recommends, not a blank slate. That is the difference between a property that holds its standard and one that resets every time someone walks out the door.

Efficiency is not a nice-to-have beside accountability — it is what makes the whole thing last. A tool that first helps you do your job, and saves your manager the morning spent assigning it, earns the right to also record it. A tool that only records gets abandoned by the second week.

## The bet: accountability without surveillance

**Proof that protects the person who collected it.**

Holding a team accountable and refusing to build a watch-tool are not in tension — one is the goal, the other is the only way to reach it. A tool built to catch and punish gets gamed: staff fill hollow forms, share phones to dodge blame, stop collecting real proof. You cannot hold anyone genuinely accountable on data they are motivated to fake. Fear makes people accountable to the watcher, not to the standard.

A tool the worker trusts gets used honestly, because it helps them — and honest data is the only thing real accountability can run on. When Priya cleans a room at 9am and the tenant complains at 4pm, the photo and the timestamp are *her* defense, not the owner's accusation. The owner sees the exception; the staff sees their record. Same data, but who it belongs to is the whole product. So a founder gets a **more** accountable team from a tool the team trusts than from one the team fears.

That is the bet, and it holds even where it costs us. **We will not ship a built-in fines system this cycle or next.** The moment staff believe the tool can dock their pay, they stop filling it honestly, and the proof collapses for everyone — including the owner who was paying for it. A watch-tool that no one fills is worth less than an honest tool that everyone does. Anti-surveillance is not a soft value bolted onto the mission; it is the mechanism that makes the mission work.

## What has to ship for the bet to hold

Two capabilities carry the bet, and nothing else matters if they slip. Together they are one outcome; as a plan they are two builds.

**One — a task knows the real thing it is about, and rules run themselves.** A task can be tied to a rent due, a tenant, a room, or an asset. When a due is paid, the module reads its state and **suggests** the task is done; the person closes it. A manager sets a standing rule once — "clean every vacant room daily until it is filled," "follow up on every under-notice tenant weekly until they move out" — and the work appears for whoever matches, each cycle, and stops when they stop matching. When a check finds a problem, the module hands it into a **complaint with the details already written**, and the person raises it. Every one of these is a suggestion a human confirms. This is what no competitor can match, and it is ship-blocking this cycle.

**Two — the business creates the task on its own.** Beyond rules the operator sets by hand, the property's own events make work appear — a move-out notice creates the deposit-inspection task, ready and assigned. This reaches into more parts of RentOk and is scheduled for the sprint right after this cycle — named, not quietly deferred.

Around those two, the redesign gives the module what every operations tool already has and ours does not, and the bet needs several of these to *be* the bet: **proof collected as part of the work** — location, time, signature, photo, all owned by the staff member first — this is the headline, not a footnote; the staff member's own record; a manager's **review loop** (approve, reject with a reason, send back); **due dates and reminders** that chase late work; a **comment thread** where staff and manager talk on a task, with the same person-tagging used elsewhere in RentOk; the module's **access control**, finally built, so each level sees what it should; a first cut of **insight** — what is failing and where; and the **checklist template library** that lets a manager set up the right tasks without a blank box, with RentOk recommending the ones a property like hers should run. The library is one deliverable inside this redesign, not a project beside it.

The PRD lists every one of these as a numbered requirement with its own test. This brief only claims the shape.

## How the tasks show up — three sources, one place

The module carries three kinds of task, and puts them where people already look.

- **The system raises it** — "rent overdue," "KYC pending" — the alerts RentOk already surfaces from the data. These stay as they are.
- **A person assigns it** — the manager's scheduled and one-off work, and the standing rules. This is the redesign.
- **A person keeps it for themselves** — a self-to-do or a log, with a reminder. "Collect keys from 204 tomorrow." Private, light, no approval.

For the manager, all three appear in the **same list she already checks, under the same categories** (Money, People, Compliance, Property, and the rest) — one place, whether the system raised the task or she did. The staff who do the physical work reach their own tasks through the runner, their working surface. Filling that manager list out to the full set of system alerts is a later phase, tracked separately so it does not weigh down this cycle.[^registry]

Setting up standing rules should feel like something managers already do. They run message and feedback campaigns today — pick who it is about, pick a cadence, set it once, and it runs itself against whoever matches. A standing rule is the same idea applied to work. So we start with a **short, curated set of useful rules RentOk recommends and ships ready to switch on** — not a rule-builder to configure. The power to express anything comes later, through a different door (below), so the everyday operator never meets a developer tool.

## What we will not build this cycle

- **Fines, salary deductions, or a staff scorecard** — the bet forbids it. Staff who feel the tool can cost them money stop filling it honestly.
- **Anything acting on its own** — the module suggests and pre-fills; a person always confirms. No task closes itself, no complaint is raised without someone raising it, no standing rule is switched on without an operator turning it on.
- **A free-form rule builder** — an "if this, then that" editor is a developer tool, not an operator tool. Curated, recommended rules this cycle; open-ended power comes through the assistant, later.
- **Attendance and shift-clocking** — a different product; folding it in blurs what this module is for.
- **Rebuilding move-out** — the move-out inspection is already a task that records deposit deductions correctly. We reuse it as the pattern; we do not touch it.
- **A native Task tab in the mobile app** — the runner and the manager's list reach the app through the existing web view, not a new tab.

Each is a thing someone will argue for. Each is out because the user does not need it this cycle — not because it is hard.

## What success looks like

**At launch, on the first real property:**
- A manager sets up tasks from the template library without a blank box, switches on a recommended standing rule, and the right work reaches the right staff on its own.
- A task tied to a rent due suggests closing when the due is paid, and a failed check hands the manager a ready-filled complaint — both wait for a person to confirm.
- A staff member on a shared phone, on a weak connection, completes a task with proof, loses the network mid-way, and does not lose the work — and can see their own record of what they did.
- The founder opens one view and sees which of his properties are keeping to the standard.

**Six months on:**
- Staff completion rate holds or rises — the honest test of whether the anti-surveillance bet is working.[^3]
- Managers run their properties from the module instead of their heads, and founders hold managers to a standard they can finally see.

## What this makes possible next

Once tasks know the real thing they are about, and rules run themselves, the module becomes the place a property's whole operating rhythm lives — and two horizons open.

The property starts to run its own standards without being told to, twice: the event work that begins next cycle makes tasks appear from what is happening, and the standing rules keep quality up before it slips.

And the biggest one — **you build any of it by talking.** RentOk's assistant, grounded in the real property, lets an operator say "clean all vacant rooms on the second floor every day, and if it's not done by 6pm, tell me" — and the rule is built, drafted for the operator to confirm. This is where the open-ended power lives without a developer tool ever appearing on screen, and it is the accessibility unlock for the exact people the bet is about: Priya and Ramu, who read Hindi better than English, can *speak* the work into existence. A generic task app's assistant can build "a task"; it cannot build "a task for all vacant rooms on the second floor," because it does not own the rooms. We do. To keep that door open, this cycle builds task and rule creation as something the assistant can later call — not a screen it can never reach.

That is the horizon: a property that runs its own standards, holds its own people accountable through proof they trust, and can be operated by talking to it — the tool the staff want, not the tool the owner imposes.

---

[^owner]: The multi-property owner maps to RentOk's "Rajesh/Priya-owner" persona set; the on-site "Priya" in this brief is the *manager* persona, a distinct role. Named to keep the accountability chain clear, not to introduce new personas.

[^1]: Priya (on-site manager) is a composite persona from RentOk's Persona Bible (`icp_and_personas.md`). Surveillance fear sourced verbatim: line 178 — "If Priya sees RentOk as a surveillance tool that threatens her job, she will sabotage adoption. Must be positioned as 'your assistant that makes the owner trust you more.'" Line 234 names "Threatened Manager (Priya)" as a top-3 deal-blocker.

[^2]: Ramu (guard) is a composite persona from the same Persona Bible. Sourced verbatim: line 192 — "If the app replaces his paper register, he may feel threatened. Position as 'modern security tools that make your job respected.'"

[^3]: Success measure is a launch A/B on staff completion rate plus qualitative interviews at week 4, comparing properties positioned "proof protects you" against a control. The claim that trust drives completion is the bet under test — measured, not assumed.

[^registry]: Building the manager's list out to the full set of system-detected tasks (the 65-entry pending-tasks registry) is a separate later phase — GitHub issue eazyapp-tech/rentok-backend#6249, which links the registry specification.

## Changelog

- **2026-07-21 (d)** — Raised the mission's why one level: a property that ran on the manager's memory now runs on a system, so it does not break when a person is busy, away, or gone. Named the continuity payoff — new hires inherit the routine, the property survives the manager's leave, new properties start from recommended routines — as the answer to staff churn.
- **2026-07-21 (c)** — Rebalanced enablement-first. Named the everyday problem (people wait to be told, even for their own job; managers burn mornings assigning it) and made the module's first job "tell people what to do" — the routine runs itself, in every direction (self and reports), via recurring tasks, standing rules, recommendations, and reminders. Restructured the mission as three jobs — tell → prove → see — one loop, help not punishment. Accountability now sits as the trust layer of "prove," not the dominant theme.
- **2026-07-21 (b)** — Expanded to the full mission: the module makes every level of the business accountable and efficient to the level below (founder → manager → staff → self), and encodes standards that run themselves and maintain quality before it slips. Reconciled accountability with the anti-surveillance bet (honest data beats coerced data). Added standing rules framed as the "campaign" model operators already know, the three task sources in one place, and the assistant ("build by talking") as the horizon where open-ended power lives without a developer tool. Curated recommended rules this cycle; free-form builder explicitly out.
- **2026-07-21 (a)** — Corrected the core model: a task tied to a real thing reads its state and *suggests* closing (a person confirms); nothing acts on its own; a failed check hands a pre-filled complaint to the manager. Separated system-raised vs manager-created vs self-to-do tasks. Filed the 65-registry build-out as a later-phase issue (#6249).
- **2026-07-20** — First rewrite into the house vision-brief form; library reframed as one deliverable inside the redesign; code stripped to the companion Audit.
