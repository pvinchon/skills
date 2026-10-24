# specs

Spec-driven development for Claude Code. A goal becomes an immutable spec; then separate agents
write the tests, check them, implement, and check the implementation. How to build, run and test a
repo comes from that repo's own skills and docs; these skills only say what to do.

## Workflow

1. **Define.** Start a session with `/spec-define <goal>`. It researches the repo, asks only the
   questions that matter, one at a time, and writes `specs/<yyyymmddhhmmss>_<slug>/spec.md`.
   Approving the spec freezes it.
2. **Build.** In the same session or a new one, `/spec-build <id>` runs the four roles in order,
   each in its own subagent, and loops on review findings until both checks pass. Or run any role
   yourself, in any session.

| Skill | Does | Effort |
|---|---|---|
| `spec-test-write` | Writes tests from the spec alone, blind to the implementation, tagged `spec:<id>/R<n>`. | high |
| `spec-test-check` | Checks that the tests cover the spec and actually test something. | xhigh |
| `spec-impl-write` | Implements until the tests pass, without touching the tests or the spec. | high |
| `spec-impl-check` | Checks that the code is correct for the spec and doesn't game the tests. | xhigh |

The four roles always run in a subagent that doesn't see the conversation that invoked it, so even
in a single session the agent that checks is never the one that wrote. The checks have no
file-writing tools.

`spec-define` and `spec-build` run only when you type them. Claude can start the four roles
itself, which is how `spec-build` calls them.

## Rules

- **The spec is a migration.** Once approved, only its `status` changes (`implemented`,
  `superseded`); a change is a new spec with `supersedes`.
- **Nothing extra lands in the repo.** Roles hand off through their final message, and `spec-build`
  keeps its copy of the frozen tests in a temp directory. The repo gains the spec, the tests and the
  code.
- **`spec-build` checks before it starts.** It must find how to run the tests and checks and see
  them pass before anything is written.
- **Its gates are commands:** every requirement has a tagged test, the tests fail before the
  implementation, the frozen tests and the spec stay unchanged, and the suite and checks pass.
- **Own tests while working, the full suite when done.**
- **Blocked means stop.** A role that can't find how to run the tests, or an implementer that thinks
  a test is wrong, replies `BLOCKED:` and `spec-build` stops for you.
