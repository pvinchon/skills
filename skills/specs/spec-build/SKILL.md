---
name: spec-build
description: Take an approved spec in specs/ from tests to a reviewed implementation, running spec-test-write, spec-test-check, spec-impl-write and spec-impl-check each in its own subagent and looping on review findings. Use when asked to build or implement a spec, or to run its whole flow.
argument-hint: <spec id>
disable-model-invocation: true
---

# Build spec $0

You orchestrate and write no tests or code yourself. Each role is a skill that runs in its own subagent with no memory of this conversation or of the other roles, so the agent that writes is never the one that checks. Invoke each with the Skill tool, with the spec id first in its arguments and your notes after it. Each returns its result as its final message; nothing is handed over through files.

The gates below are commands: run them and act on their output rather than judging by eye.

Each loop runs in batches of 5 rounds. When a batch ends without a PASS, show the user the latest findings and ask whether to run 5 more; stop if they decline or no one can answer.

## 0. Preflight, before anything is written

1. The spec is `specs/$0_*/spec.md` and must be `status: approved`; otherwise stop.
2. Find out how this repo runs tests and checks, from its skills, CLAUDE.md or AGENTS.md, Makefile or package scripts, or CI: how to run given test files, how to run the full suite, and the other checks (lint, typecheck, build).
3. Run the full suite and the other checks once. If you can't find them, they don't run, or they already fail, stop and tell the user what is missing: a baseline that fails can't tell a regression from what was already broken.
4. Run `mktemp -d` and write the path it prints wherever `<tmp>` appears below; shell variables don't carry over between commands. Then `cp <spec> <tmp>/spec.md`.

The notes you pass to every role are those three commands, plus the findings of the last round when there are any.

## 1. Tests

1. Invoke `spec-test-write`. If its reply starts with `BLOCKED:`, stop.
2. Gates:
   - The spec is untouched: `cmp -s <spec> <tmp>/spec.md`. If not, restore it with `cp`, stop, and tell the user.
   - The test files are `git grep --untracked -l "spec:$0/" -- ':!specs'`.
   - Every requirement is tagged and no tag names a requirement the spec lacks: `grep -oE '^- R[0-9]+\.' <spec> | grep -oE 'R[0-9]+' | sort -u` and `git grep --untracked -hoE "spec:$0/R[0-9]+" -- ':!specs' | sed 's|.*/||' | sort -u` print the same list.
   - Red: the spec's tests fail, and because the behaviour is missing, not from a syntax, import or setup error.
3. If every gate passes, invoke `spec-test-check`. `PASS` on its first line ends the loop. Otherwise its findings, or the failed gates, are the next round's notes.

## 2. Freeze the tests

`tar cf <tmp>/tests.tar <test files>` and `git hash-object <test files> | tee <tmp>/tests.hash`.

## 3. Implementation

1. Invoke `spec-impl-write`. If its reply starts with `BLOCKED:`, stop.
2. Gates:
   - The tests and the spec are untouched: `git hash-object <test files> | diff - <tmp>/tests.hash` and `cmp -s <spec> <tmp>/spec.md`. If not, restore them (`tar xf <tmp>/tests.tar`, `cp`), stop, and tell the user.
   - The spec's tests pass, then the full suite and the other checks pass.
3. If every gate passes, invoke `spec-impl-check`, noting that the tests and checks passed on this tree. `PASS` on its first line: set `status: implemented` in the spec's front matter and finish. Otherwise its findings, or the failed gates, are the next round's notes.

## Finish

Remove `<tmp>`. Report the spec, the rounds each loop took, the test files and what changed; or, if you stopped, why, with the latest findings.
