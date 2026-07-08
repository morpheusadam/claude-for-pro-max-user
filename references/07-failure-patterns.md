# Failure Patterns

Five recurring failure modes. Detect early; each has a fixed remedy.

## 1. Kitchen Sink Session

Symptom: task A, unrelated question, back to task A. Context carries irrelevant weight; quality drops on both.
Remedy: `/clear` between unrelated tasks; `/btw` for side questions.

## 2. Correction Spiral

Symptom: wrong output, correction, still wrong, correction again. Context is now polluted with failed approaches feeding subsequent attempts.
Remedy: two-strike rule. After two failed corrections, `/clear` and write a better initial prompt encoding what was learned. Clean session + better prompt beats long session + accumulated corrections.

## 3. Over-Specified CLAUDE.md

Symptom: file so long that half is ignored; important rules lost in noise. More text produces less compliance.
Remedy: ruthless pruning. Delete any rule the model follows without being told. Convert critical rules to deterministic hooks.

## 4. Trust-Then-Verify Gap

Symptom: plausible-looking implementation that fails on edge cases, accepted because it looks right.
Remedy: always attach verification - tests, script, screenshot. If it cannot be verified, it does not ship.

## 5. Infinite Exploration

Symptom: unscoped "investigate X" causes hundreds of file reads, filling context before any work starts.
Remedy: scope investigations narrowly, or delegate to a subagent so exploration cost stays out of main context.

## Judgment Layer

The patterns above are defaults, not laws. Deviations are justified:

- Let context accumulate when deep in one complex problem where history is load-bearing.
- Skip planning when the task is exploratory by nature.
- Use a vague prompt deliberately to see unconstrained interpretation before narrowing.

Calibration method: when output is excellent, identify what produced it - prompt structure, context provided, mode. When output struggles, identify why - noisy context, vague prompt, oversized task. The resulting intuition supersedes any static guide.

## Pro Operator Checklist

- CLAUDE.md short, pruned, in git, containing only non-guessable rules
- Critical rules as hooks, not prose
- Every non-trivial task has a runnable check (test/build/screenshot)
- Heavy research in subagents, not main context
- Multi-file task -> plan first; one-sentence diff -> direct execution
- Two failed corrections -> reset with improved prompt
- Independent workstreams -> parallel worktrees
- Repetitive work at scale -> `claude -p` + `--allowedTools`
- Unattended work -> adversarial review in fresh context before acceptance
- Evidence (test output, screenshots) over claims of success
