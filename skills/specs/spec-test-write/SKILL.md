---
name: spec-test-write
description: Write the tests for an approved spec in specs/, from the spec alone and blind to any implementation. Runs in its own subagent. Use when asked to write the tests for a spec, or from spec-build.
argument-hint: <spec id> [notes]
context: fork
agent: general-purpose
effort: high
background: false
---

# Write the tests for spec $0

Arguments: `$ARGUMENTS`. The first is the spec id; anything after it is notes from the caller, such as how to run tests or review findings to fix.

The spec is `specs/$0_*/spec.md` and must be `status: approved`. Read it; never edit it.

## Before writing

Know how to run a given set of test files in this repo, from the notes or from the repo's skills, CLAUDE.md or AGENTS.md, Makefile or package scripts, or CI. If you can't find out, reply `BLOCKED:` with what is missing and stop.

## Rules

- Inputs: the spec, the repo's testing skill or conventions, and existing tests for their patterns and fixtures. Do not read or rely on code that implements this spec's requirements.
- Cover every acceptance criterion, test plan entry and behaviour or edge-case row, at the levels the spec names.
- Tag every test with `spec:$0/R<n>` for each requirement it covers, in its name or a comment. Every requirement needs at least one.
- Assert the behaviour the spec states, through the real interface (the endpoint, the table, the function), and nothing it doesn't state. Deterministic: no real time, randomness or network unless injected.
- Put the tests in files that hold only tests: spec-build freezes them whole before implementation, so a file that mixes tests and code would lock the implementer out. Where the repo keeps tests inside source files, use the nearest form that gives them a file of their own.
- The tests must fail for the right reason: the behaviour is missing. If symbols must exist for them to compile, add minimal stubs in implementation files that fail with `not implemented: spec:$0`.
- Fix every review finding in the notes. Existing tests may be strengthened, never weakened or deleted.
- Run only this spec's tests while you work. When you believe you are done, run the full suite: only this spec's tests may fail.

## Reply

Your final message is the hand-off; write no other files. List the test files and the stubs, and anything the implementer needs to know.
