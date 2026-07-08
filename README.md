# claude-for-pro-max-user

A Claude Code skill that installs a professional operating protocol. On load, Claude switches to absolute-mode communication (no filler, no emojis, no soft asks, no call-to-action endings) and applies expert agentic engineering discipline distilled from Anthropic's official best practices: context engineering, verification loops, plan-first execution, delegation, and failure-pattern avoidance.

## What it changes

- **Communication**: stripped-down, directive, high-density output. Responses end at the last piece of requested material. No questions, no offers, no engagement optimization.
- **Execution**: scope -> explore -> plan -> implement -> verify -> evidence, on every non-trivial task. Nothing is reported as done without a runnable check.
- **Context discipline**: aggressive rationing of the context window, subagent delegation for research, two-strike reset rule on failed corrections.
- **Escalation judgment**: knows when a rule belongs in CLAUDE.md vs a hook vs a skill vs a subagent, and applies verification gates proportional to autonomy.

## Structure

```
SKILL.md                              Master protocol: absolute-mode instruction layer,
                                      core operating loop, standing rules, reference map
references/
  01-context-engineering.md           Context as the scarcest resource; tool selection table
  02-claude-md-engineering.md         Persistent memory: include/exclude, imports, maintenance
  03-verification-loops.md            Pass/fail gates, gate escalation ladder, adversarial review
  04-explore-plan-code.md             Phase protocol, prompt interpretation, interview pattern
  05-scale-automation.md              Headless mode, fan-out, worktrees, CI, permission strategy
  06-extending-claude.md              Hooks vs skills vs subagents vs MCP decision table
  07-failure-patterns.md              Five failure modes, remedies, operator checklist
```

SKILL.md loads first. Reference files load on demand per its reference map, keeping per-session context cost minimal.

## Installation

Personal (all projects):

```bash
git clone https://github.com/morpheusadam/claude-for-pro-max-user.git ~/.claude/skills/pro-max-operator
```

Project-scoped:

```bash
git clone https://github.com/morpheusadam/claude-for-pro-max-user.git .claude/skills/pro-max-operator
```

## Usage

- Automatic: Claude applies the protocol when the user requests professional behavior, pro mode, or absolute mode.
- Manual: `/pro-max-operator`
- Permanent: add to `CLAUDE.md`:

```markdown
- YOU MUST load and follow the pro-max-operator skill in every session.
```

## Sources

- [Claude Code best practices (official)](https://code.claude.com/docs/en/best-practices)
- [How Claude Code is used in practice - Anthropic research](https://www.anthropic.com/research/claude-code-expertise)
- [Claude Code power user tips](https://support.claude.com/en/articles/14554000-claude-code-power-user-tips)

## License

MIT
