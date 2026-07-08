# Scale and Automation

One effective session is the baseline. Multiply throughput with parallel sessions, non-interactive mode, and fan-out. Teams routinely run 4-8 concurrent worktrees per developer.

## Headless Mode

`claude -p "prompt"` runs without a session. Foundation for CI pipelines, pre-commit hooks, cron jobs, GitHub Actions.

```bash
claude -p "Explain what this project does"
claude -p "List all API endpoints" --output-format json
claude -p "Analyze this log file" --output-format stream-json --verbose
claude -p "<prompt>" --output-format json | downstream_command
```

`--verbose` for development, off in production.

## Fan-Out (large migrations/analyses)

1. Generate a task list ("list all Python files needing migration" -> files.txt).
2. Loop:

```bash
for file in $(cat files.txt); do
  claude -p "Migrate $file from React to Vue. Return OK or FAIL." \
    --allowedTools "Edit,Bash(git commit *)"
done
```

3. Test on 2-3 files, refine the prompt from what failed, then run the full set.

`--allowedTools` scopes permissions - mandatory for unattended runs.

## Parallel Sessions

Ordered by coordination overhead:

| Method | Mechanism |
|---|---|
| Git worktrees | `claude --worktree name` - isolated checkout per session, zero collision |
| Desktop app | Visual management of local sessions, each in its own worktree |
| Claude Code on the web | Anthropic-managed isolated VMs |
| Agent teams | Automated multi-session coordination: shared tasks, messaging, team lead |

Parallelism serves quality, not only speed: a fresh session reviews without bias toward code it wrote (Writer/Reviewer, see verification reference).

## Permission Strategy for Autonomy

Approval fatigue is a failure mode - after the tenth prompt the operator clicks without reviewing. Three mechanisms:

1. **Auto mode**: `claude --permission-mode auto -p "fix all lint errors"` - a classifier reviews commands, blocking scope escalation, unknown infrastructure, hostile-content-driven actions. In `-p` mode, repeated blocks abort the run.
2. **Allowlists** (`/permissions`): pre-approve known-safe commands (`npm run lint`, `git commit`).
3. **Sandboxing** (`/sandbox`): OS-level filesystem and network isolation.

## CI Integration Pattern

```yaml
- name: Claude triage
  run: |
    claude -p "Review this PR diff for security issues.
    Output JSON: {severity, file, line, description} per finding." \
      --output-format json \
      --allowedTools "Read,Grep,Glob,Bash(git diff *)" \
      > findings.json
```

Common applications: issue triage, PR labeling, log analysis, release notes.

## Unattended Work Acceptance Rule

The longer the unattended run, the stronger the required independent check before accepting results:

- Minimum: `/code-review` on the final diff.
- Better: adversarial subagent comparing diff against plan.
- Long runs: agent team sustaining the work -> review -> fix loop, operator spot-checks recorded findings.
