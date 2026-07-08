# Verification Loops

Without a runnable check, "looks done" is the only stop signal and the operator becomes the verification loop. With a check that returns pass/fail, the loop closes autonomously: work, run check, read result, iterate until pass.

A check is anything that yields a readable signal: test suite, build exit code, linter, script diffing output against a fixture, browser screenshot compared to a design.

## Directives

- Never declare a task complete without executing its check.
- Never suppress an error to make a check pass. Address the root cause.
- Report evidence, not assertions: test output, executed command and its result, screenshot. Failures are reported plainly with the failing output.
- If no check exists and none can be constructed, state that the work is unverified.

## Prompt Transformation Patterns

| Weak | Strong |
|---|---|
| "implement a function that validates email addresses" | "write validateEmail. Test cases: user@example.com true, invalid false, user@.com false. Run the tests after implementing" |
| "make the dashboard look better" | "[screenshot] implement this design. Screenshot the result, compare to the original, list differences, fix them" |
| "the build is failing" | "build fails with [error]. Fix it and verify the build succeeds. Address the root cause, do not suppress the error" |

## Gate Escalation Ladder

Setup cost rises, supervision cost falls:

1. **In-prompt**: run the check and iterate within the same task. Works immediately on any task.
2. **Session goal** (`/goal`): a separate evaluator re-checks the condition after every turn; work continues until it holds.
3. **Stop hook**: a script gates turn completion; the turn cannot end until the check passes (override after 8 consecutive blocks prevents deadlock).
4. **Second opinion**: a fresh-context subagent or workflow attempts to refute the result. The agent that did the work does not grade it.

## Writer/Reviewer Pattern

Fresh context reviews better - no bias toward code it just wrote.

- Session A implements. Session B (fresh) reviews the artifact against edge cases, race conditions, and existing patterns. Session A applies the feedback.
- Test variant: one session writes tests, a second writes code to pass them.

## Adversarial Review

Before accepting unattended or long-running work:

```
Use a subagent to review the diff against PLAN.md. Check every requirement
is implemented, listed edge cases have tests, and nothing outside the task's
scope changed. Report gaps, not style preferences.
```

The reviewing subagent returns gaps directly to the implementing session for fix and re-review.

Calibration: a reviewer prompted to find gaps will report some even when the work is sound. Chasing every finding produces over-engineering - extra abstraction, defensive code, tests for impossible cases. Only findings affecting correctness or stated requirements are actionable; the rest are optional.

## Rule

If it cannot be verified, it does not ship.
