# Claude Code Companion Starter

[中文说明](./README.zh-CN.md)

Turn Claude Code into a more consistent, workspace-aware, long-term personal assistant.

A bootstrap prompt starter for people who want Claude Code to do more than answer one-off questions. It helps Claude Code establish a durable collaboration layer through global rules in `~/.claude/` and project-local memory in `.assistant/`.

## Why This Exists

Many "personal assistant" prompts look good on paper but become unreliable in real CLI workflows. Common problems include:

- over-relying on chat history that vanishes between sessions
- mixing long-term rules with temporary project notes
- treating generated file content as if it were real file operations
- forcing heavy startup behavior on every task

This repository is designed around those realities.

Instead of assuming the model will remember everything, it uses the file system as a durable collaboration surface that Claude Code can re-read and update over time.

## Why OpenClaw-Inspired Memory For Claude Code

Claude Code is strong at following instructions, reading repositories, and editing files. But that does not automatically make it a long-term collaboration system.

OpenClaw-style memory systems are useful because they treat files as durable memory artifacts rather than leaving everything in chat history. That idea maps well to Claude Code:

- stable preferences belong in structured files
- project decisions should survive across sessions
- temporary notes should stay separate from long-term memory

This repository adapts that philosophy to Claude Code by combining:

- `~/.claude/` for global behavior defaults
- `.assistant/` for project-local memory and collaboration context

### Global Layer `~/.claude/`

| File | Purpose |
| --- | --- |
| `CLAUDE.md` | Entry file, `@` imports other rules (auto-merges if `@` not supported) |
| `assistant-core.md` | Role behavior, read order, conflict resolution, quick review entry |
| `bootstrap-rules.md` | Cold start rules: tiered questions, state tracking, completion persistence |
| `memory-policy.md` | Memory write / conflict / cleanup / audit rules |
| `project-filesystem.md` | Project file structure definition, Git safety handling |
| `global-*.md` | Global user profile, style, workflow, and reusable memory |
| `global-projects-index.md` | Auto-maintained index of all initialized projects and recent sessions |

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
  sync-policy.md     — global memory sync strategy (ask / always / never)
  memory/
    daily/           — short-lived daily context (YYYY-MM-DD.md)
    projects/        — project-level cross-session memory
  templates/         — reusable starter templates
  runtime/
    inbox.md         — short-lived action items
    last-session.md  — last session summary
```

The result is not "turning Claude Code into OpenClaw". The result is giving Claude Code a more structured memory surface so it can feel less stateless and more like a repeatable collaborator.

## What This Changes

- preferences and collaboration habits stop living only in chat history
- project context becomes inspectable and editable
- temporary context and durable memory are stored separately
- Claude Code can re-enter a workspace with better continuity

## Concrete Examples

1. Style becomes stable across sessions.  
   Preferences such as "be concise, give the conclusion first, then risks" can live in `.assistant/STYLE.md`.

2. Project decisions stop resetting.  
   If a project already decided to postpone a large refactor, that can live in `.assistant/memory/projects/architecture.md`.

3. Temporary context stops polluting permanent rules.  
   One-day notes or unverified facts can live in `.assistant/memory/daily/YYYY-MM-DD.md`.

4. Workflow becomes personalized.  
   If you prefer "inspect first, explain the change briefly, then report verification", that can live in `.assistant/WORKFLOW.md`.

## How It Works

This starter uses two layers:

| Layer | Path | Purpose |
| --- | --- | --- |
| Global | `~/.claude/` | behavior defaults, read order, bootstrap rules, memory policy |
| Project | `.assistant/` | user profile, style, workflow, project memory, daily notes |

Claude Code reads the durable file structure instead of depending only on ephemeral conversation state.

## What's Included

- [claude-code-companion-bootstrap.md](./claude-code-companion-bootstrap.md)  
  The main bootstrap prompt for Claude Code.

- [README.zh-CN.md](./README.zh-CN.md)  
  Chinese documentation.

- [LICENSE](./LICENSE)  
  MIT license.

## Quick Start

1. Open [claude-code-companion-bootstrap.md](./claude-code-companion-bootstrap.md).
2. Copy the full `text` code block.
3. Start Claude Code in your target workspace.
4. Send the prompt in one message.
5. Let Claude Code inspect the workspace, update config, and start lightweight bootstrap if needed.

## How This Prompt Is Positioned

This repository provides one main prompt starter, not multiple variants.

It is best understood as a structured full setup version:

- proactive enough to initialize global and project layers
- explicit about file creation and verification
- opinionated about memory structure and bootstrap flow

## Built-in Optimizations

The prompt includes 15 production-hardened optimizations:

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
14. HOME directory global-only mode (uses `~/.claude/` directly, skips `.assistant/`)
15. Global Memory Promotion (syncs reusable knowledge from projects to global memory with 4 strategies: sync once / keep local / always sync / never sync)

## Design Principles

- Global defaults should guide behavior, not claim permanent hard control
- Project-local memory should carry evolving context
- Temporary notes should not pollute long-term memory
- Incremental edits are safer than destructive rewrites
- Real CLI behavior matters more than idealized agent theory

## Important Boundaries

- `~/.claude/` should be treated as a default behavior layer, not a guaranteed permanent control layer.
- `.assistant/` is a project-local memory convention, not a native Claude Code protocol.
- Actual behavior still depends on runtime context, available tools, and higher-priority instructions.

## FAQ

### Why not just use `CLAUDE.md`?

Because behavior rules and collaboration memory are different things.

`CLAUDE.md` is good for stable defaults. But project history, user preferences, temporary notes, and unfinished work fit better in a small memory structure.

### Doesn't `.assistant/` add too much overhead?

It can, if overbuilt.

This starter keeps the structure intentionally small. The goal is not bureaucracy. The goal is reducing repeated friction in ongoing work.

### Is this trying to replace Claude Code?

No.

This is a prompt-layer workflow enhancement. Claude Code still does the actual work. This repository simply gives it a more durable collaboration structure.

### Who is this for?

It is a good fit for people who use Claude Code repeatedly in the same projects and want more continuity across sessions.

It is less useful if you only want quick one-off prompts with no local memory structure.

## Notes

If Claude Code behaves too aggressively in your environment, tighten the prompt or reduce the initialization scope.

If it is too passive, strengthen the execution instructions around file creation, verification, and bootstrap follow-through.
