---
name: spec-impl-write
description: Implement an approved spec in specs/ until its tests pass, without touching the tests or the spec. Runs in its own subagent. Use when asked to implement a spec whose tests exist, or from spec-build.
argument-hint: <spec id> [notes]
context: fork
agent: general-purpose
effort: high
background: false
---

# Implement spec $0

Arguments: `$ARGUMENTS`. The first is the spec id; anything after it is notes from the caller, such as how to run tests or review findings to fix.

The spec is `specs/$0_*/spec.md`. Its tests are the files tagged `spec:$0/` (`git grep --untracked -l "spec:$0/" -- ':!specs'`). Read both; edit neither.

## Before writing

Know how to run given tests, the full suite, and the repo's other checks (lint, typecheck, build), from the notes or from the repo's skills, CLAUDE.md or AGENTS.md, Makefile or package scripts, or CI. If you can't find out, reply `BLOCKED:` with what is missing and stop.

## Rules

- Read the tests to see what they exercise (the endpoint, the table, the function), and implement that, following the spec's Interfaces & Data and Implementation Constraints.
- Make the behaviour correct for the spec, not just for the tested examples: no special-casing of test inputs, hardcoded expected values, or detecting a test run. A reviewer checks for this.
- Replace the stubs that fail with `not implemented: spec:$0`.
- Follow the repo's conventions and patterns. Light refactoring that the change needs is fine; unrelated changes are not.
- If a test seems wrong or contradicts the spec, don't touch it: reply `BLOCKED:` with the evidence and stop.
- Fix every review finding in the notes.
- While you work, run only this spec's tests. When you believe you are done, run the full suite and the other checks, and fix anything you broke.

## Reply

Your final message is the hand-off: what changed, and the result of the final full run.
