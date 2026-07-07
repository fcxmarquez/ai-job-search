# Codex Compatibility Layer

This repository was originally authored for Claude Code. In Codex, preserve the existing workflow source of truth by routing slash-command style requests to the matching files in `.claude/commands/`.

## Command Routing

When the user invokes or refers to one of these commands, read the corresponding command file before acting:

| User command | Read this file |
| --- | --- |
| `/setup` | `.claude/commands/setup.md` |
| `/apply` | `.claude/commands/apply.md` |
| `/scrape` | `.claude/skills/job-scraper/SKILL.md` |
| `/rank` | `.claude/commands/rank.md` |
| `/outcome` | `.claude/commands/outcome.md` |
| `/expand` | `.claude/commands/expand.md` |
| `/add-template` | `.claude/commands/add-template.md` |
| `/add-portal` | `.claude/commands/add-portal.md` |
| `/reset` | `.claude/commands/reset.md` |

If the command includes arguments, treat those arguments as the original `$ARGUMENTS` referenced by the Claude command file.

## Codex Adaptation Rules

- Treat `.claude/commands/*.md` as workflow instructions, not literal Codex slash-command implementations.
- Treat `.claude/skills/**/SKILL.md` as reusable workflow context. The same skill folders are symlinked under `.agents/skills` for Codex discovery.
- Translate Claude tool names to Codex equivalents:
  - `Read`, `Glob`, and `Grep` -> inspect files with normal filesystem tools.
  - `Edit` and `Write` -> use `apply_patch` for manual edits.
  - `WebFetch` and `WebSearch` -> use available Codex web/browser tools when current external data is required.
  - `Agent tool` -> use a Codex subagent or the custom `research-reviewer` agent when explicitly needed.
  - `AskUserQuestion` -> ask the user directly only when a required decision cannot be made safely.
- Keep the original workflow order unless Codex tooling requires a small mechanical translation.

## Reviewer Agent

For company research and application critique, use the project Codex custom agent `research-reviewer` when a subagent is appropriate. If subagents are unavailable or unnecessary, perform the same review pass in the main Codex thread and clearly separate the draft, review, revision, and verification steps.
