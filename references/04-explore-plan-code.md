# Explore -> Plan -> Code -> Commit

Jumping straight to code risks solving the wrong problem. Separate research and planning from implementation.

## Phase Protocol

1. **Explore** (plan mode / read-only): read the relevant modules; answer how the current system works before proposing changes.
2. **Plan**: enumerate files to change, the approach, the session flow, and the verification step. The plan is editable by the operator before execution (`Ctrl+G` opens it in an editor).
3. **Implement**: execute the plan, write tests, run the suite, fix failures.
4. **Commit**: descriptive message, PR when requested.

## Skip Rule

Planning has overhead. If the diff can be described in one sentence (typo fix, log line, rename), execute directly. Plan when: approach is uncertain, change spans multiple files, or the code area is unfamiliar.

## Prompt Interpretation Standards

Precision in equals precision out. Apply and encourage these transformations:

| Weak | Strong |
|---|---|
| "add tests for foo.py" | "write a test for foo.py covering the logged-out edge case. Avoid mocks" |
| "why does ExecutionFactory have a weird api?" | "read ExecutionFactory's git history and summarize how its api came to be" |
| "add a calendar widget" | "study how existing widgets are implemented (HotDogWidget.php is a good example). Follow that pattern for a calendar widget with month select and year pagination. No new libraries" |
| "fix the login bug" | "login fails after session timeout. Check auth flow in src/auth/, especially token refresh. Write a failing test reproducing the issue, then fix it" |

Vague prompts are legitimate for exploration ("what would you improve in this file?"). Vague for exploration, precise for execution.

## Interview Pattern (large features)

Instead of receiving a spec, extract one:

```
I want to build [brief description]. Interview me in detail using the
AskUserQuestion tool. Ask about technical implementation, UI/UX, edge cases,
concerns, and tradeoffs. Skip obvious questions; dig into the hard parts.
Keep interviewing until everything is covered, then write a complete spec to SPEC.md.
```

Execute the spec in a fresh session: clean context, written reference. A good spec is self-contained - names files and interfaces, states what is out of scope, ends with an end-to-end verification step. Time spent sharpening the spec returns more than time spent watching implementation.

## Rich Context Channels

- `@file` references instead of descriptions of where code lives.
- Pasted screenshots for visual bugs and design targets.
- URLs for documentation; allowlist frequent domains via `/permissions`.
- Piped data: `cat error.log | claude`.
- Self-fetching: pull context via Bash, MCP tools, or file reads rather than asking for it.

## Codebase Onboarding

Answer senior-engineer questions directly, with file:line references: how logging works, how to add an endpoint, what edge cases a class handles, why line 333 calls foo() instead of bar(). Cite the git history when the question is about how an API came to be.
