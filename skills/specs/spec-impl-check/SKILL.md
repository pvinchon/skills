---
name: spec-impl-check
description: Adversarial review of a spec's implementation by an agent that did not write it, confirming it is correct for the spec and not gamed to pass the tests. Runs in its own subagent with no file-writing tools. Use when asked to check or review the implementation of a spec, or from spec-build.
argument-hint: <spec id> [notes]
context: fork
agent: general-purpose
effort: xhigh
background: false
disallowed-tools: Write, Edit, NotebookEdit
---

# Check the implementation of spec $0

Arguments: `$ARGUMENTS`. The first is the spec id; anything after it is notes from the caller.

You did not write this code. Be adversarial. Change nothing: your verdict is your final message.

The spec is `specs/$0_*/spec.md`. Its tests are the files tagged `spec:$0/` (`git grep --untracked -l "spec:$0/" -- ':!specs'`). Find out how to run the tests and the repo's other checks from the notes or from the repo's skills, CLAUDE.md or AGENTS.md, Makefile or package scripts, or CI.

## Check

1. Tests and checks: run the spec's tests, the full suite and the other checks, unless the notes say they passed on this tree.
2. Correct: walk every requirement, edge-case row and acceptance criterion, and confirm the behaviour in the code, not just in the tests. Try at least one input the tests don't use.
3. Not gamed: no special-casing of test inputs or fixtures, hardcoded expected values, detection of a test run, stubs or mocks left in place of the real thing, or behaviour that only holds for the tested examples.
4. Interfaces and data match the spec exactly: names, signatures, payloads, status and error codes, schemas, migrations up and down.
5. Implementation Constraints respected; forbidden approaches absent.
6. Scope: `git status` and `git diff` show nothing unrelated to the spec beyond light refactoring the change needed, and the tests and the spec are untouched.
7. The spec's Validation Checklist, item by item.

## Reply

First line exactly `PASS` or `FAIL`. Then numbered findings, each with the requirement id, the evidence (file:line), and the fix wanted. PASS only with zero findings.
