# CLAUDE.md Engineering

CLAUDE.md loads at the start of every conversation. Two consequences: every line costs context in all sessions; a well-maintained file compounds in value. Bloated files cause instruction loss - the model ignores rules buried in noise.

## Retention Test

For each line ask: "Would removing this cause mistakes?" If no, delete it. If the model already behaves correctly without the instruction, the instruction is noise.

## Include / Exclude

| Include | Exclude |
|---|---|
| Bash commands that cannot be guessed | Anything inferable by reading code |
| Style rules that differ from language defaults | Standard conventions the model already knows |
| Test instructions and preferred runners | Full API documentation (link instead) |
| Repository etiquette (branch naming, PR rules) | Frequently changing information |
| Project-specific architectural decisions | Long tutorial-style explanations |
| Environment quirks (required env vars) | File-by-file codebase descriptions |
| Non-obvious gotchas | Self-evident practices ("write clean code") |

## Reference Form

```markdown
# Code style
- ES modules (import/export), not CommonJS
- Destructure imports where possible

# Workflow
- Typecheck after any series of code changes
- Run single tests, not the whole suite, for performance

# Gotchas
- IMPORTANT: never touch legacy/ - deprecated but live in production
- Migrations only via `npm run db:migrate`, never raw SQL

# Compaction
- When compacting, preserve modified file list and test commands
```

- Emphasis markers (`IMPORTANT`, `YOU MUST`) measurably improve adherence. Reserve them for critical rules.
- No required format. Short and human-readable.

## Imports

`@path` syntax composes files:

```markdown
See @README.md for project overview and @package.json for available npm commands.
- Git workflow: @docs/git-instructions.md
- Personal overrides: @~/.claude/my-project-instructions.md
```

Team rules in shared files; personal preferences in home-directory imports.

## Placement Hierarchy

| Location | Scope | Use |
|---|---|---|
| `~/.claude/CLAUDE.md` | All projects | Personal global preferences |
| `./CLAUDE.md` | Project, in git | Team rules; team contributes |
| `./CLAUDE.local.md` | Project, gitignored | Personal project notes |
| Parent directories | Monorepo | Root and subdirectory files both auto-load |
| Child directories | On demand | Loads only when files in that directory are read |

Monorepo pattern: general rules at root, service-specific rules in each service directory - context spent only when relevant.

## CLAUDE.md vs Skill

- Applies to every session -> CLAUDE.md.
- Domain knowledge or workflow needed occasionally -> Skill. Skills load on demand and cost nothing in other sessions.

## Maintenance Protocol

- Rule violated despite being present -> file is too long; the rule is lost. Prune.
- Questions asked whose answers are in the file -> phrasing is ambiguous. Rewrite.
- Rule must hold 100% of the time -> convert to a hook and delete the prose line. Hooks are deterministic; prose is advisory.
- Test edits by observing whether behavior actually shifts.
- Keep the file in git.
