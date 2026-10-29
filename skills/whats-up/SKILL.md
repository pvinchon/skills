---
name: whats-up
description: Status report on the work in this session — what's done, what's in progress, what's next, and what is waiting on you — then carry on with anything that isn't
disable-model-invocation: true
---

# What's up

Report where the work in this conversation stands, written so the user can pick the context back up cold. Then make sure you are not sitting idle on something that doesn't actually need them.

## 1. Re-read anything that changes on its own

A PR's checks, review threads and review decision, a background agent or command, a deploy: look again rather than repeating what it showed earlier, since that reading is from then. A background agent that hasn't reported is still running; say so and never guess its result. Keep this to quick reads; the report is what was asked for.

## 2. Sort what remains

An item waits on the user only when it needs a decision only they can make, information only they have, or authorization for something irreversible or outward-facing. Everything else is yours: a choice with a sensible default, a credential not yet looked for under other names, a fact you can look up, a "want me to…?" an earlier turn ended on. Those go under **Next**.

An item waiting on a third party (CI, a reviewer, a teammate) goes under **In progress**, naming who it waits on.

## 3. Report

Four headings, in this order, each a tight list:

- **Done**: outcomes, not the route to them. Link every PR, doc and run. Mark anything that ran but that you haven't seen working as unverified: a command exiting 0 or a deploy starting shows the action ran, not that it had its effect.
- **In progress**: what is moving right now, including background work, and what each item waits on.
- **Next**: in order, with what each depends on.
- **Waiting on you**: each item written as if it had never been mentioned, ending in a question answerable in one word. If there are none, write "Nothing".

The user asked because they may have lost the thread, so name everything by what it is: no plan codes, no terms coined during the session, and no function or file names unless the code itself is the topic.

## 4. Keep going

If **Next** holds work the user already asked for and nothing blocks it, carry on with it in the same turn rather than ending on the report. When that work stops, close with the **Waiting on you** items again in full, since the report will have scrolled out of view.

## Rules

- **Never start work the user hasn't asked for.** In a planning session the next step is still theirs to call; the report says what it would be and stops.
- **The report is as long as the current state, not the session.** A long session still gets a short **Done**.
