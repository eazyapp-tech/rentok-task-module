# Pending tasks: home-screen needs-attention

**This folder is the source of truth for this pack.** Obsidian and Notion are mirrors later. Where they disagree, this repo wins.

This is the manager **home-screen needs-attention feed**: the cards from `getPendingTasks` (route `GET /v1/home/analytics/pending-tasks`). A manager opens the home screen and sees what still needs a human: overdue rent, unmatched payments, a booking with no token, a payout that failed.

**This is not the Task module.** The rest of this repo is checklist · task · schedule: people doing named work on a cadence, with proof. Do not mix the two. A needs-attention card *counts* something from another table and asks the manager to tap through. A Task-module task is work someone is meant to complete.

**Home is one card at a time.** Cards sit in a stack (cover-flow). You can rotate them. Swipe left and right. They do not all show on home at once.

**View All is the hub.** Full-screen, pending tasks grouped by category: Money, People, Compliance, Property, Daily Ops, Growth, Platform. The catalog lives here. Every eligible card can show here.

This pack documents the **hub catalog** and the **stack ranking**. If you treat every card as “on home at once,” you are wrong.

The parent dump is [`sources/pending-tasks-registry.md`](sources/pending-tasks-registry.md). **Do not edit that file.** Cards copy what it claims. They are not a fresh code check. Live-vs-code lives only in [`GAPS.md`](GAPS.md).

---

## How to read

1. **[`INDEX.md`](INDEX.md).** The View All catalog. Every ID, one line. Everyday name, claimed status, link to the card.
2. **[`RULES.md`](RULES.md).** How the stack picks which card is on top. View All groups by category instead.
3. **[`NEED-YOU.md`](NEED-YOU.md).** All locked 16 Aug — open the file.
4. **[`GAPS.md`](GAPS.md).** Live vs claimed. ~10 types can enter the live stack; Money on new home is A2 only.

Category files hold one card per ID, situation first.
