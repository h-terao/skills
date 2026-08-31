---
name: tdd
description: Test-driven development workflow for implementing or changing production code. Use when adding a feature, fixing a bug, or changing behavior. Not needed for documentation, configuration, or pure refactors already covered by tests.
---
# Test-Driven Development Workflow

Work in small red-green-refactor cycles, one behavior per cycle.

## Cycle

1. **Red** — Write one test describing the next behavior (or reproducing the bug). Run it and confirm it fails *for the expected reason* — a failure from a typo or broken setup doesn't count as red.
2. **Green** — Write the minimum code to pass. No speculative generality, no handling of cases no test demands yet.
3. **Refactor** — With tests green, improve structure, naming, and duplication in the code you just touched. Keep tests passing throughout; don't refactor unrelated areas.

## Rules

- Never get to green by weakening, skipping, or deleting a test. If a test itself seems wrong, flag it and fix the test deliberately as its own step.
- Hardcoding to satisfy a test is fine mid-cycle only if the next test forces the real implementation.
- Test behavior through public interfaces, not implementation details.
- Delegate test runs to the `test-runner` agent to keep this context focused on implementation.

## Post-implementation pruning

Once the implementation is complete and everything is green, review the tests you added and prune the ones that won't catch future regressions. For each test ask: *would this fail if someone broke real behavior later?* If not, delete or strengthen it. Typical candidates:

- Tests that only re-verify library or framework behavior instead of your own code.
- Weak string assertions (e.g., substring `in` checks). Decide by where the string comes from:
  - The exact text is specified behavior (user-facing message, API contract, format required by the spec) → strengthen to an exact or structured assertion.
  - You authored the text yourself and the wording is incidental (e.g., an LLM prompt you drafted) → asserting on it only pins your own draft and breaks on harmless rewording. Delete the test, or keep assertions only on the parts that carry a real requirement (e.g., dynamic values are interpolated, a required section is present).
- Intermediate scaffolding tests now fully subsumed by a stronger test.

This is distinct from the no-deletion rule above: pruning happens only while all tests are green, never as a way to get there.

## Scope

- Skip TDD for docs, config, or behavior-preserving refactors under existing coverage — there, just keep tests green.
- If the code has no test seam, first do a minimal behavior-preserving refactor to create one, then start the cycle.
