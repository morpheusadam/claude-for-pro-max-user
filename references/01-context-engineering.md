# Context Engineering

Governing constraint: the context window fills fast and model performance degrades as it fills. The window holds every message, every file read, every command output. A single debugging session can consume tens of thousands of tokens. Manage context as the primary resource.

## Directives

- Treat every file read and command output as spent budget. Read the minimum necessary section of a file, not the whole file.
- Delegate any investigation expected to touch many files to a subagent. Subagents run in a separate context window and return only a summary. The main context stays clean for implementation.
- After completing a task, recommend `/clear` before an unrelated task. Never let two unrelated workstreams share one context.
- After two failed corrections on one issue, stop iterating. The context now contains failed approaches that contaminate subsequent attempts. A clean session with a rewritten prompt that encodes the lessons learned outperforms continued correction.
- When compaction is imminent, preserve deliberately: modified file list, test commands, key decisions. Support directed compaction: `/compact keep only API changes and modified files`.
- Codify compaction behavior persistently in CLAUDE.md: "When compacting, always preserve the full list of modified files and any test commands."

## Tool Selection Table

| Situation | Action |
|---|---|
| New unrelated task | `/clear` |
| Long task, history still needed | `/compact <instructions>` |
| Two failed corrections | `/clear` + rewritten prompt |
| Heavy codebase research | Subagent |
| Quick side question | `/btw` (never enters history) |
| Risky experiment | Attempt, then `/rewind` on failure |
| Fact needed in every session | CLAUDE.md |
| Domain knowledge needed occasionally | Skill (loads on demand) |
| Multi-day workstream | Named session (`/rename`) + `--resume` |

## Checkpoints

- Every prompt creates a checkpoint. `Esc Esc` or `/rewind` restores conversation, code, or both to any checkpoint.
- Partial compaction: "Summarize from here" condenses forward from a checkpoint; "Summarize up to here" condenses history while keeping recent messages intact.
- Checkpoints permit risk: attempt aggressive approaches, rewind on failure. Checkpoints persist across sessions.
- Checkpoints track only changes made by Claude, not external processes. They do not replace git.

## Sessions

- Treat sessions like branches: one persistent context per workstream, named descriptively (`oauth-migration`).
- `claude --continue` resumes the latest session; `claude --resume` selects from a list.

## Anti-Pattern

Kitchen-sink session: task A, unrelated question, back to task A. Context now carries irrelevant weight and quality drops. Side question belongs in `/btw`; new task belongs after `/clear`.
