---
name: spec-test-check
description: Adversarial review of a spec's tests against the spec, by an agent that did not write them. Runs in its own subagent with no file-writing tools. Use when asked to check or review the tests for a spec, or from spec-build.
argument-hint: <spec id> [notes]
context: fork
agent: general-purpose
effort: xhigh
background: false
disallowed-tools: Write, Edit, NotebookEdit
---

# Check the tests for spec $0

Arguments: `$ARGUMENTS`. The first is the spec id; anything after it is notes from the caller.

You did not write these tests. Be adversarial. Change nothing: your verdict is your final message.

The spec is `specs/$0_*/spec.md`. The tests are the files tagged `spec:$0/` (`git grep --untracked -l "spec:$0/" -- ':!specs'`). Find out how to run them from the notes or from the repo's skills, CLAUDE.md or AGENTS.md, Makefile or package scripts, or CI.

## Check

1. Coverage: every requirement, acceptance criterion and edge-case row has a test, and no tag names a requirement the spec lacks.
2. They test something: each test would fail against a stub or a plausibly wrong implementation. Flag assertion-free and no-op tests, tautologies, mocks of the very thing under test, snapshot-everything tests, and tests that check a side channel instead of the real effect (the response, the row, the return value). For each requirement, name one plausible bug the tests would miss, or say there is none.
3. Fidelity: nothing asserted that the spec doesn't state, and nothing that contradicts it.
4. Determinism: no hidden dependence on time, ordering, randomness or network.
5. Right failure: run the spec's tests. They must fail because the behaviour is missing, not from syntax, import or setup errors.

## Reply

First line exactly `PASS` or `FAIL`. Then numbered findings, each with the requirement id, the evidence, and the fix wanted. PASS only with zero findings.
