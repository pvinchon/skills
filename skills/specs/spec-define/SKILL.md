---
name: spec-define
description: Turn a goal into an immutable spec (specs/<timestamp>_<slug>/spec.md) through research and a short interview, ready for spec-build or the spec-test and spec-impl skills. Use when asked to define or spec out a feature before building it, or to turn a goal, ticket or notes into a spec.
argument-hint: <goal text | ticket URL/ID | path to notes>
disable-model-invocation: true
---

# Define a spec

Input: `$ARGUMENTS` (a written goal, a ticket reference, or a file path). If empty, ask for the goal.

You produce `specs/<id>_<slug>/spec.md`, where `<id>` is the creation time as `yyyymmddhhmmss` (`date +%Y%m%d%H%M%S`). Other agents write the tests, implement and review from it alone, each in a fresh context, so it must be unambiguous, testable and self-contained.

Work like a principal engineer: the goal is to get the work done, not to run an exhaustive interview. Research answers most questions, and the repo's conventions and patterns answer most of the rest. Decide those yourself and record them under Decisions. Light refactoring that the change needs is fine.

## Asking questions

- Ask only what is truly important and has no obvious answer. No nitpicking.
- Never ask what you can look up: the repo, its skills and docs, CLAUDE.md and AGENTS.md, the ticket, git history, other specs, and the web if available.
- One question at a time, with 2-4 concrete options, each with a one-line trade-off, and one marked `★ Recommended` with the reason, citing what you found. Use a choice UI if available.
- With each question, say roughly how many you still expect to ask ("about 3 left"). Follow-ups that an answer raises aren't counted.
- Record each answer in the draft at once. If it contradicts an earlier answer or a finding, say so.
- "You decide" takes the recommendation; log it under Decisions as `(delegated)`.

## 1. Intake

- Resolve the goal: fetch the ticket, read the file, or take the text.
- Create the spec file with `status: draft` and restate the goal in 1-2 sentences. The spec file is the working draft; it changes freely until approved.

## 2. Clarify the goal

Establish the outcome (what is true after this ships that isn't now), who benefits and why, how success is measured, and the non-goals. Research first; ask only the residue. Read the goal back in one paragraph and get a yes.

## 3. Research (no user input)

- The repo's own conventions: its skills, CLAUDE.md and AGENTS.md, and how it builds, runs and tests.
- Code: affected modules, existing patterns, similar features, current tests, public interfaces, data models and migrations.
- Prior art: other specs, ADRs, docs, TODOs, closed PRs and issues.
- External: library and API docs, standards or regulations the goal names.

Note conflicts, risks, and anything the goal assumes that the code contradicts.

## 4. Settle the details

Walk the draft for every decision an implementer would otherwise guess: behaviour and error paths, edge inputs, interfaces, data and migrations, security, rollout. Settle each from research and conventions, and ask only where the answer is genuinely open and matters. Vague words ("fast", "appropriate", "etc.") are defects to remove.

## 5. Write the spec

Use exactly this structure:

```
---
id: <yyyymmddhhmmss>
slug: <slug>
title: <title>
status: draft            # draft | approved | implemented | superseded
created: <YYYY-MM-DD>
source: <ticket/URL/"user prompt">
supersedes: []           # ids of specs this replaces
---

# <title>

## 1. Goal
One paragraph. Outcome, who benefits, why.

## 2. Non-goals
Explicit exclusions.

## 3. Context
Relevant current state with file paths/links. Only what an implementer needs.

## 4. Decisions
| # | Decision | Alternatives rejected | Rationale |
Every choice made, including `(delegated)` ones.

## 5. Requirements
Numbered, atomic, testable. Use RFC 2119 (MUST/SHOULD/MAY).
- R1. The system MUST ...
Each requirement has exactly one observable pass/fail condition.

## 6. Interfaces & Data
Exact signatures, schemas, payloads, error codes, migrations (up and down).

## 7. Behavior & Edge Cases
Table: scenario | input/state | expected result | requirement ids.
Include error, boundary, concurrency, and failure-midway cases.

## 8. Acceptance Criteria
Given/When/Then, each mapped to requirement ids. Every requirement is covered by at least one criterion; every criterion traces to a requirement.

## 9. Test Plan
Per criterion: level (unit/integration/e2e), fixtures/data, what to assert. Written so a test-writing agent needs nothing else.

## 10. Implementation Constraints
Required patterns, files likely touched, forbidden approaches, dependencies allowed/denied, rollout/flag plan.

## 11. Validation Checklist
Checks beyond the repo's usual tests, lint and build that a reviewer runs to declare done (manual checks, migration up and down).

## 12. Open Questions
Must be EMPTY before status: approved.
```

Write each requirement on its own line in exactly the form `- R<n>. <text>`, with no bold or other markup: spec-build finds requirements by that prefix, and tests are tagged `spec:<id>/R<n>`.

## 6. Self-audit, then approval

Before presenting, check and fix:
- [ ] Every requirement is atomic, testable, and has an acceptance criterion
- [ ] Every acceptance criterion maps to a test plan entry
- [ ] No vague or unmeasurable language; no TBDs; Open Questions empty
- [ ] Interfaces and data are exact, not illustrative
- [ ] Nothing contradicts the codebase or another spec
- [ ] A fresh agent with only this file and the repo could implement and validate it

Present a short summary (goal, number of requirements, key decisions, anything delegated) and ask for approval. On approval set `status: approved` and give the path and the next step: `/spec-build <id>`, here or in a new session, or the four roles one at a time (`spec-test-write`, `spec-test-check`, `spec-impl-write`, `spec-impl-check`).

## Immutability

- Once `approved`, a spec is never edited except for `status` changes.
- A change is a NEW spec with `supersedes: [<id>]`; mark the old one `superseded`.
- Implemented specs are never deleted.
- Write no implementation code in this skill.
