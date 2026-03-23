# Claude Recall

> Have a mind like a steel trap.
> 长期记忆，断点中继。

[中文说明](./README.zh-CN.md)

> **Looking for other CLI assistants?** 
> Check out the sister extensions: [Gemini Recall](https://github.com/d-wwei/gemini-recall) & [Codex Recall](https://github.com/d-wwei/codex-recall)

Turn Claude Code into a long-term collaboration system with durable memory and session continuity.

A deployment-ready package for people who want Claude Code to do more than answer one-off questions. It emphasizes long-term memory, growing collaboration chemistry over time, session handoff continuity, and fast recall of prior context.

## Why This Exists

Many "personal assistant" prompts look good on paper but become unreliable in real CLI workflows. Common problems include:

- over-relying on chat history that vanishes between sessions
- mixing long-term rules with temporary project notes
- treating generated file content as if it were real file operations
- forcing heavy startup behavior on every task

This repository is designed around those realities.

Instead of assuming the model will remember everything, it uses the file system as a durable collaboration surface that Claude Code can re-read and update over time.

## Key Features

This prompt transforms Claude Code from a powerful but amnesic one-shot tool into a long-term collaborator that builds rapport with you.

- **Long-term Memory Setup:** Automatically establishes persistent global rules (`~/.claude/`) and project-specific memory (`.assistant/`).
- **Preference Accumulation:** Remembers how you like things done—your role, coding style, workflow, and recurring instructions.
- **Session Handoff Continuity:** Keeps work resumable across interruptions, so the next session can continue from the right breakpoint instead of restarting from scratch.
- **Global Memory Promotion:** As it discovers your habits and reusable knowledge during active projects, it can automatically promote them to your global profile.
- **Project-Specific Customization:** Every project can have its own tailored rules and context that override the global defaults.
- **Fast Recall:** Helps Claude quickly recall your background, working style, recent decisions, and unfinished context when you re-enter a workspace.
- **Proactive Context Loading:** At every session start, Claude silently checks `active-task.md` and briefly mentions any in-progress or blocked task — no need to say "continue" first.
- **Unified Session Recovery:** A single `active-task.md` file serves as the recovery anchor — containing current task state, execution context, and interrupted task queue — replacing multiple scattered runtime files.
- **Session Archive & Search:** Completed sessions are auto-archived to `memory/sessions/` with an `INDEX.md` for fast historical lookup by date, project, or tags.
- **Task-Level Resume Checkpoints:** For multi-step work of many kinds, Claude can maintain a module-local `PROGRESS.md` so a new process can resume from the last accepted step or milestone instead of reconstructing progress from chat.
- **Layered Bootstrap Interview:** The first-round interview now follows a compact 3-step script that captures name, style, assistant role, ambiguity handling, work types, and memory boundaries without turning startup into a form.
- **Global Quick Mode:** When launched from `$HOME`, Claude initializes only the global layer and writes directly to `~/.claude/` without asking whether to sync those same facts again.
- **Historical Project Scan:** On first-time setup, Claude can scan prior `.assistant/` workspaces and summarize discovered projects and sessions into the global index.

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
    sessions/        — auto-archived session summaries + INDEX.md for fast search
  templates/         — reusable starter templates
  runtime/
    inbox.md         — short-lived action items
    last-session.md  — last session summary
    active-task.md   — current task state + execution context + interrupted queue (single recovery anchor)
```

For non-trivial ongoing work, Claude should also maintain a short `PROGRESS.md` in the active module or task directory. That file is task-local, not part of the shared `.assistant/` skeleton.

The result is not "turning Claude Code into OpenClaw". The result is giving Claude Code a more structured memory surface so it can feel less stateless and more like a repeatable collaborator.

## Fast Recall Protocol

The latest recall flow separates workspace-level interruption routing from module-level `PROGRESS.md` checkpoints.

- `.assistant/runtime/active-task.md` keeps the current highest-priority main task.
- `.assistant/runtime/interrupted-tasks.md` keeps the rest of the paused queue in priority order.
- `.assistant/runtime/resume-protocol.md` defines the hard rules for the first recovery reply.
- `.assistant/runtime/resume-checkpoint-template.md` provides a reusable schema for named handoff checkpoints.

When the user says things like `continue`, `resume`, `刚才做到哪里了`, or `恢复刚才的任务`, Claude should first read `active-task.md`, then reply in three compact sections:

```text
A. 当前主任务
task: ...
progress: ...
next step: ...

---

B. 其他中断任务
task: ...
priority: P2
progress: ...
next step: ...

---

C. 恢复选项
1. 继续当前主任务
2. 切换到 P2 ...
3. 切换到 P3 ...
```

This makes recovery faster, keeps the first response readable, and lets the user switch directly into another paused task without a long explanation pass first.

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

5. Interrupted work can resume cleanly.  
   A nearby `PROGRESS.md` can record which milestones or acceptance items are done, what is currently in progress, and what the next concrete step should be for the next Claude process.

Use the generic template by default for content, video, research, operations, design, or mixed project work:

```md
status: in_progress
task: Produce launch video cut
module_path: content/video-launch/
project_type: video-editing

# 任务进度

## 已完成
- [x] 确认视频目标、时长和发布渠道
- [x] 整理可用素材与配音版本

## 进行中
- [ ] 精剪主版本时间线并对齐字幕

## 待做
- [ ] 输出 16:9 主版本
- [ ] 裁切 9:16 短视频版本
- [ ] 完成最终审校并导出交付文件

## 关键决策
- 主版本控制在 90 秒内，优先保留产品演示镜头
- 字幕风格统一使用品牌模板，避免重新设计一套样式

## 已知问题
- 第三段配音底噪偏重，可能需要降噪或重录

## 关键文件 / 素材
- raw/interview-a-roll/
- edits/launch-main.prproj
- assets/subtitles/final-cn.srt
```

Keep the development-specific template for software implementation work:

```md
status: in_progress
task: Add task-level progress recovery
module_path: packages/assistant-memory/

# 开发进度

## 已完成
- [x] 明确 `PROGRESS.md` 使用模块局部文件而不是 `.assistant/`
- [x] 加入恢复口令：继续上次进度 / 恢复进度
- [x] 定义恢复时的候选定位顺序

## 进行中
- [ ] 把恢复逻辑接入当前模块的初始化流程

## 待做
- [ ] 补充恢复话术模板
- [ ] 增加多候选 `PROGRESS.md` 的确认逻辑
- [ ] 完成一次中断恢复流程验证

## 关键决策
- `PROGRESS.md` 放在实际模块目录，避免所有任务共用一份中心状态文件
- 恢复时只读取最相关的 1-2 个候选，减少 token 消耗

## 已知问题
- 当前模块还没有验证“多个候选进度文件”时的选择行为
```

Use the generic template for milestone-based work. Use the development template when the task is driven by explicit acceptance items and code verification. For explicit recovery, the user can say `继续上次进度`, `恢复进度`, `resume progress`, or `continue from progress`.

When recovering, the assistant should locate the most relevant `PROGRESS.md` in this order: current working directory, most recently modified module, user-named module, then best keyword-matching module. It should read only the top 1-2 candidates instead of scanning every progress file in the repo.

The recommended resume wording is:

```text
我找到了这份进度记录：
- 已完成：...
- 进行中：...
- 下一步：...

要我按这份进度继续吗？
```

## How It Works

This starter uses two layers:

| Layer | Path | Purpose |
| --- | --- | --- |
| Global | `~/.claude/` | behavior defaults, read order, bootstrap rules, memory policy |
| Project | `.assistant/` | user profile, style, workflow, project memory, daily notes |

Claude Code reads the durable file structure instead of depending only on ephemeral conversation state.

## What's Included

- [BOOTSTRAP_PROMPT.md](./BOOTSTRAP_PROMPT.md)  
  The main bootstrap prompt you send to Claude Code.

- [RESET_GUIDE.md](./RESET_GUIDE.md)  
  Reset and recovery guide for rebuilding the memory system.

- [scripts/show-bootstrap.sh](./scripts/show-bootstrap.sh)  
  Prints the bootstrap prompt locally.

- [scripts/show-reset.sh](./scripts/show-reset.sh)
  Prints the reset guide locally.

- [scripts/export-session.sh](./scripts/export-session.sh)
  Auto-archive Claude Code session summaries (can be used as a hook or run manually).

- [README.zh-CN.md](./README.zh-CN.md)
  Chinese documentation.

- [LICENSE](./LICENSE)  
  MIT license.

## Deploy

1. Clone or download this repository.
2. Open `BOOTSTRAP_PROMPT.md`, or print it with:

```bash
./scripts/show-bootstrap.sh
```

3. Start Claude Code in the target workspace.
4. Paste the prompt into Claude Code in one message.
5. Let Claude inspect the workspace, initialize memory, and begin lightweight bootstrap.

## Package Layout

```text
BOOTSTRAP_PROMPT.md
RESET_GUIDE.md
scripts/
  show-bootstrap.sh
  show-reset.sh
  export-session.sh
```

This repository is now organized as a lightweight Claude deployment pack rather than only a nested prompt file.

## Quick Start

1. Open [BOOTSTRAP_PROMPT.md](./BOOTSTRAP_PROMPT.md).
2. Copy the full prompt.
3. Start Claude Code in your target workspace.
4. Send the prompt in one message.
5. Let Claude Code inspect the workspace, update config, and start lightweight bootstrap if needed.

## Recent Improvements

- **proactive context loading** — Claude checks `active-task.md` at every session start and briefly mentions in-progress work
- **unified session recovery** — single `active-task.md` replaces `interrupted-tasks.md`, `resume-protocol.md`, and `resume-checkpoint-template.md`
- **session archive system** — `memory/sessions/` with `INDEX.md` for historical session lookup
- **multi-mode recall** — supports `#tag` conventions for categorizing and filtering recall context
- **export-session.sh** — new script for auto-archiving Claude Code session summaries (usable as a hook)
- richer first-round user interview for long-term collaboration
- explicit global quick mode behavior under `$HOME`
- task-level `PROGRESS.md` checkpoints for interrupted implementation work
- global memory promotion with 4 sync strategies

## How This Prompt Is Positioned

This repository provides one main prompt starter, not multiple variants.

It is best understood as a structured full setup version:

- proactive enough to initialize global and project layers
- explicit about file creation and verification
- opinionated about memory structure and bootstrap flow

## Built-in Optimizations

The prompt includes 19 production-hardened optimizations:

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
16. Module-local `PROGRESS.md` checkpoints for interrupted task recovery
17. Proactive context loading (silently checks active task at session start)
18. Unified `active-task.md` as single recovery anchor (replaces 4 separate runtime files)
19. Session archive with indexed history (`memory/sessions/INDEX.md`)

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
