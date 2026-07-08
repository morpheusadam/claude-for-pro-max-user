# Extension Mechanism Selection

Six extension mechanisms exist. Using the wrong one for a problem is a common failure (e.g., a must-always-hold rule written as prose instead of a hook).

## Decision Table

| Problem | Mechanism | Reason |
|---|---|---|
| Action must happen 100% of the time | Hook | Deterministic; prose is advisory |
| Domain knowledge needed occasionally | Skill | Loads on demand, zero cost otherwise |
| Task reads many files / needs isolated focus | Subagent | Separate context, scoped tools |
| External service integration | CLI tool first, MCP second | CLI is more context-efficient |
| Rule applying to every session of a project | CLAUDE.md | Auto-loaded each session |
| Packaging any of the above for distribution | Plugin | Skills + hooks + agents + MCP in one unit |

## Hooks

Deterministic gates at lifecycle points. Configured in `.claude/settings.json`; browsable via `/hooks`. Claude writes hooks on request ("write a hook that runs eslint after every file edit"; "write a hook that blocks writes to the migrations folder").

| Hook point | Standard uses |
|---|---|
| PreToolUse | Block writes outside repo; redact secrets from shell commands |
| PostToolUse | Formatter/linter after every file edit |
| Stop | Run test suite; turn cannot end until pass |

## Subagents

Defined in `.claude/agents/`:

```markdown
---
name: security-reviewer
description: Reviews code for security vulnerabilities
tools: Read, Grep, Glob, Bash
model: opus
---
You are a senior security engineer. Review code for:
- Injection vulnerabilities (SQL, XSS, command injection)
- Authentication and authorization flaws
- Secrets or credentials in code
- Insecure data handling
Provide specific line references and suggested fixes.
```

- Restrict `tools` to the minimum: a reviewer does not need Write.
- `model` is set per agent: heavy analysis to a stronger model, mechanical work to a cheaper one.
- Subagents can run in isolated worktrees for parallel batched file mutations.

## Skills

Two distinct uses:

**Domain knowledge** (auto-invoked when relevant):

```markdown
---
name: api-conventions
description: REST API design conventions for our services
---
- kebab-case URL paths, camelCase JSON properties
- Pagination on all list endpoints
- Version in URL path (/v1/, /v2/)
```

**Parameterized workflow** (manual invocation only):

```markdown
---
name: fix-issue
description: Fix a GitHub issue
disable-model-invocation: true
---
Analyze and fix the GitHub issue: $ARGUMENTS.
1. `gh issue view` for details
2. Locate relevant files
3. Implement the fix
4. Write and run tests
5. Push and create a PR
```

`disable-model-invocation: true` for workflows with side effects (push, deploy) that must never self-trigger. The `description` field determines auto-invocation; write it as trigger conditions, not marketing.

## CLI over MCP

CLI tools are the most context-efficient external integration. GitHub -> `gh`. Cloud -> `aws`, `gcloud`. Monitoring -> `sentry-cli`. Unknown tool -> "run `foo --help`, learn it, then do A, B, C". Reserve MCP for services without good CLIs: databases, Figma, Notion, browser automation. `claude mcp add <name>`.

## Rule Maturation Path

```
repeated observation -> CLAUDE.md line -> (if 100% compliance required) -> hook, delete the prose line
```
