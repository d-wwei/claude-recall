# Claude Code Companion Starter

[中文说明](./README.zh-CN.md)

Turn Claude Code into a consistent, workspace-aware, long-term personal assistant — not a one-shot Q&A tool.

This is a one-click bootstrap prompt that transforms Claude Code by:

- writing persistent global rules to `~/.claude/`
- initializing structured project memory in `.assistant/`
- running a lightweight bootstrap conversation to capture your preferences
- making every future workspace entry feel continuous instead of starting from scratch

**One run is all it takes.** After that, Claude Code will automatically initialize any new project and remember how you work.

## Quick Start

1. Open [`claude-code-companion-bootstrap.md`](./claude-code-companion-bootstrap.md).
2. Copy the full `text` code block (~210 lines).
3. Send it to Claude Code in your target workspace.
4. Claude Code will:
   - Set up `~/.claude/` with global assistant rules (with `@` import compatibility check)
   - Create `.assistant/` project memory structure
   - Start a bootstrap conversation (how to address you, your role, preferred style)

> **One-time setup.** Global rules persist permanently — every new project gets auto-initialized from then on. You never need to send the prompt again.

## What the Prompt Does

### Global Layer `~/.claude/`

| File | Purpose |
| --- | --- |
| `CLAUDE.md` | Entry file, `@` imports other rules (auto-merges if `@` not supported) |
| `assistant-core.md` | Role behavior, read order, conflict resolution, quick review entry |
| `bootstrap-rules.md` | Cold start rules: tiered questions, state tracking, completion persistence |
| `memory-policy.md` | Memory write / conflict / cleanup / audit rules |
| `project-filesystem.md` | Project file structure definition, Git safety handling |

### Project Layer `.assistant/`

Auto-created per project:

```
.assistant/
  SYSTEM.md          — workspace-level rules and safety boundaries
  USER.md            — who the user is: name, role, context, language
  STYLE.md           — communication style preferences
  WORKFLOW.md        — how work is done
  TOOLS.md           — tool preferences and boundaries
  MEMORY.md          — concise long-term reusable memory
  BOOTSTRAP.md       — bootstrap state tracking
  memory/
    daily/           — short-lived daily context (YYYY-MM-DD.md)
    projects/        — project-level cross-session memory
  templates/         — reusable starter templates
  runtime/
    inbox.md         — short-lived action items
    last-session.md  — last session summary
```

## Core Idea: File System as External Memory

Inspired by [OpenClaw's memory model](https://docs.openclaw.ai/concepts/memory), this prompt uses the file system as a durable collaboration layer.

| Layer | Path | Purpose |
| --- | --- | --- |
| Global | `~/.claude/` | Behavior rules that apply everywhere: role, read order, conflict resolution, memory policy |
| Project | `.assistant/` | Per-project memory: user profile, style, workflow, decisions, daily context |

### Why This Matters

Without structured memory, you repeat yourself every session:
- how you want answers structured
- what role you play in the workspace
- which tradeoffs the project already made
- where the last session stopped

With `.assistant/`, these become editable project assets instead of fragile chat leftovers.

## Built-in Optimizations

The prompt includes 13 production-hardened optimizations:

1. `@` import compatibility detection + merged fallback
2. Idempotency check (3-line content rule)
3. Post-write file verification
4. Daily log lifecycle (7/14 day cleanup)
5. `last-session.md` write timing rules
6. Memory conflict resolution
7. Bootstrap completion state persistence (`status` field)
8. Question priority tiers (must-ask / should-ask / accumulate naturally)
9. User audit rights (view / delete / export / monthly reminder)
10. `.gitignore` auto-handling
11. Template system (customizable during bootstrap)
12. Quick review entry ("查看我的配置" / "review my setup")
13. Workspace confirmation (prevents `.assistant/` in non-project dirs)

## Design Principles

- Default preference layer, not hard control layer
- Incremental edits over destructive rewrites
- Project-local memory over assumed global omniscience
- Lightweight bootstrap over long onboarding forms
- Realistic compatibility with Claude Code's actual behavior

## FAQ

### Why not just use `CLAUDE.md`?

`CLAUDE.md` is great for behavior rules. But real collaboration also involves evolving preferences, project history, and temporary context. A small memory structure keeps these cleanly separated and maintainable.

### Does `.assistant/` make the workspace heavy?

Not if kept small. The prompt intentionally keeps the structure minimal and fills it incrementally. Just enough local memory to reduce repeated friction.

### Who is this for?

People who use Claude Code repeatedly in the same projects and want more continuity across sessions. Not needed for quick one-off prompts.

## License

[MIT](./LICENSE)
