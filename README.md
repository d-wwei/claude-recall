# Codex Companion Starter

[中文说明](./README.zh-CN.md)

Prompt templates for turning Codex CLI into a more consistent long-term personal assistant workflow.

This repository is for people who want Codex CLI to do more than answer one-off questions. The prompts here help it:

- keep a stable collaboration style
- initialize local project memory structure
- avoid unnecessary rewrites
- bootstrap user preferences in a lightweight way

The pack is intentionally split into three variants, so you can choose how cautious or proactive Codex should be in your environment.

## Why This Exists

Raw prompts often overfit to an idealized agent model. In practice, Codex CLI behaves better when instructions are:

- explicit about file operations
- careful about existing content
- realistic about what global config can and cannot control
- lightweight enough to avoid blocking normal work

These prompts are written around that reality.

## Why OpenClaw-Inspired Memory For Codex CLI

Codex CLI and OpenClaw are strong in different ways.

Codex CLI is naturally good at instruction layering and execution. Its `AGENTS.md` model is useful for defining behavior by scope: global defaults, repository rules, and local overrides. That makes it strong at policy and workflow control, but not inherently strong at long-lived structured memory. [Codex AGENTS.md guide](https://developers.openai.com/codex/guides/agents-md)

OpenClaw takes a different approach. Its memory model treats Markdown files as durable working memory, with daily logs and longer-term memory living in the workspace as explicit artifacts. That makes it much better at preserving collaboration context over time. [OpenClaw memory docs](https://docs.openclaw.ai/concepts/memory)

This repository borrows that memory philosophy and adapts it to Codex CLI.

The goal is not to pretend Codex CLI has native persistent memory in the same sense. The goal is to use the file system as an external memory layer, so Codex can behave less like a stateless tool and more like a repeatable long-term collaborator.

### What This Changes

- Rules and preferences stop living only in chat history.
- Project decisions can survive across sessions.
- Temporary notes and stable preferences can be separated cleanly.
- Codex can re-enter a workspace with better continuity.

### Why It Feels More Human

The improvement is not about making Codex sound warmer. It is about making collaboration feel continuous.

With only raw prompts, you often have to restate:

- how you want answers structured
- what role you play in the workspace
- which tradeoffs the project already chose
- where the last session stopped

With a memory layout such as `.assistant/STYLE.md`, `.assistant/WORKFLOW.md`, `.assistant/MEMORY.md`, and `memory/projects/*.md`, those details become editable project assets instead of fragile conversational leftovers.

That makes Codex more likely to:

- respond in your preferred format
- avoid repeating already-settled project debates
- preserve the way you like work to be pushed forward

### Why It Can Grow With The User

`AGENTS.md` is best for stable rules.

But real collaboration also includes evolving preferences, recurring habits, project-specific decisions, and temporary context. Those fit better in a memory structure than in a single instruction file.

A good external memory layer lets Codex grow with the user by gradually capturing:

- stable style preferences
- recurring workflows
- project constraints and decisions
- unfinished work from recent sessions

This is a better match for long-term use than trying to stuff everything into one global rules file.

### Concrete Examples

1. Style becomes stable across sessions.  
   Instead of repeating "be concise, give the conclusion first, then risks", those preferences can live in `STYLE.md`.

2. Project decisions stop resetting.  
   If a project already decided not to split a service yet, that can live in `memory/projects/architecture.md` instead of being re-debated from scratch.

3. Workflow becomes personalized.  
   If you prefer "inspect first, explain the edit briefly, then change files, then report verification", that can live in `WORKFLOW.md`.

4. Temporary context stops polluting long-term rules.  
   Short-lived notes such as "this API doc is still unverified" belong in `memory/daily/YYYY-MM-DD.md`, not in permanent memory.

### The Practical Meaning

This approach does not replace Codex CLI's core behavior.

It adds a structured external memory layer on top of what Codex already does well:

- Codex provides instruction loading and execution
- the file system provides durable collaboration memory
- this prompt pack connects the two

That is the real point of the project: not just making Codex act smarter, but making it easier to work with over time.

## What's Included

- [codex-cli-personal-assistant-prompt-safe.md](./codex-cli-personal-assistant-prompt-safe.md)  
  Inspect first, change later. Best for first-time setup and existing environments.

- [codex-cli-personal-assistant-prompt-lite.md](./codex-cli-personal-assistant-prompt-lite.md)  
  Balanced default for most day-to-day use.

- [codex-cli-personal-assistant-prompt-strong.md](./codex-cli-personal-assistant-prompt-strong.md)  
  More proactive setup for greenfield projects or when you want Codex to move faster.

- [codex-cli-personal-assistant-prompt-comparison.md](./codex-cli-personal-assistant-prompt-comparison.md)  
  A side-by-side comparison table.

## Quick Start

1. Open the prompt file you want to use.
2. Copy the full `text` code block.
3. Send it to Codex CLI in the target workspace.
4. Let Codex inspect the workspace, update config, and start lightweight bootstrap if appropriate.

## Which Version Should I Use?

| Situation | Recommended |
| --- | --- |
| First time trying this workflow | `safe` |
| Default long-term usage | `lite` |
| New project and you want stronger setup automation | `strong` |
| Existing workspace with a lot of hand-written config | `safe` |

## Recommended Adoption Path

1. Start with `safe` to observe how Codex behaves in your actual environment.
2. Switch to `lite` once the structure and defaults feel right.
3. Use `strong` when you explicitly want Codex to push setup forward with fewer confirmations.

## Design Principles

- Default preference layer, not hard control layer
- Incremental edits over destructive rewrites
- Project-local memory over fake global omniscience
- Lightweight bootstrap over long onboarding forms
- Realistic compatibility with Codex CLI behavior

## Important Boundaries

- `~/.codex/AGENTS.md` should be treated as a default preference layer, not a permanent hard control mechanism.
- `.assistant/` is a local project convention for memory and collaboration context, not a built-in Codex CLI protocol.
- Actual behavior still depends on runtime instructions, tool permissions, and higher-priority context.

## Who This Is For

This project is useful if you want Codex CLI to behave more like:

- a repeatable project assistant
- a workspace-aware collaborator
- a tool that can preserve lightweight context across tasks

It is probably not useful if you only want quick one-off prompts with no local structure.

## Notes

If Codex behaves too aggressively in your environment, fall back to `safe`.

If it is too conservative and does not make enough progress, move to `strong`.
