# Claude Recall

> Have a mind like a steel trap.
> 长期记忆，断点中继。

[English](./README.md)

> **正在寻找其他 CLI 助手的版本？**
> 看看兄弟扩展项目：[Gemini Recall](https://github.com/d-wwei/gemini-recall) & [Codex Recall](https://github.com/d-wwei/codex-recall)

把 Claude Code 调整成一个具备长期记忆与断点中继能力的长期协作系统。

这是一个面向 Claude Code 的轻量部署包，适合那些不满足于“一次性问答”，而希望 Claude Code 具备长期记忆、积累默契、断点中继和快速回想能力的人。

## 这个仓库解决什么问题

很多"个人助理型提示词"在纸面上很完整，但放到真实 CLI 工作流里常常不够稳定。常见问题包括：

- 过度依赖聊天记录，一换会话就失去上下文
- 把长期规则和临时项目信息混在一起
- 把输出的文件内容误当成真的文件写入
- 每次启动都做得太重，影响正常任务推进

这个仓库就是围绕这些现实问题设计的。

它不假设模型会天然记住一切，而是让 Claude Code 把文件系统当作长期协作界面的一部分，方便后续反复读取和更新。

## 核心特性快速概览

这个提示词的核心魔力在于：**把 Claude Code 从一个每次都要重新磨合的“超强单次工具人”，变成一个能长久记住你、陪你积累默契的“个人专属助理”。**

- **长期记忆体系**：自动建立跨项目的全局记忆（`~/.claude/`）和项目私有记忆（`.assistant/`）。
- **偏好与习惯积累**：记住你的身份、代码风格、工作推进流、重复的高频指令。
- **断点中继**：在会话中断、任务切换或隔天继续时，能更自然地从正确进度接上，而不是重新摸索上下文。
- **自动记忆提炼**：在具体的项目实践中，自动发掘你的习惯和可复用经验，并提供提炼到全局记忆的能力。
- **项目专属定制**：每个项目互不干扰，可以拥有自己独有的上下文结构和覆盖全局的个性化记忆。
- **快速回想**：重新进入工作区时，能更快回想起你的背景、风格、近期决策和未完成上下文。
- **主动上下文加载**：每次会话启动时，Claude 自动检查 `active-task.md`，如果有进行中的任务会主动提及——不需要你先说"继续"。
- **统一会话恢复**：单个 `active-task.md` 作为恢复锚点，包含当前任务状态、执行上下文和中断任务队列，取代了之前分散的多个 runtime 文件。
- **会话归档与检索**：完成的会话自动归档到 `memory/sessions/`，配合 `INDEX.md` 支持按日期、项目或标签快速查找历史会话。

## 为什么要用 OpenClaw 风格记忆系统改造 Claude Code

Claude Code 很擅长遵循指令、阅读仓库、修改文件，但这并不自动等于它已经具备长期协作系统的能力。

OpenClaw 风格记忆系统的价值，在于把文件视作明确的长期记忆资产，而不是把所有上下文都压在聊天历史里。这种思路特别适合 Claude Code：

- 稳定偏好应该进入结构化文件
- 项目决策应该跨会话保留
- 临时信息应该和长期记忆分开

这个仓库把这套思路落到了 Claude Code 上，通过两层结构来实现：

- `~/.claude/` 承载全局默认行为
- `.assistant/` 承载项目本地记忆和协作上下文

### 全局层 `~/.claude/`

| 文件 | 作用 |
| --- | --- |
| `CLAUDE.md` | 入口文件，`@` 导入其他规则（不支持时自动合并写入） |
| `assistant-core.md` | 角色行为、读取顺序、冲突解决、快速回顾入口 |
| `bootstrap-rules.md` | 冷启动规则：分层提问、状态追踪、完成持久化 |
| `memory-policy.md` | 记忆写入 / 冲突 / 清理 / 审计规则 |
| `project-filesystem.md` | 项目文件结构定义、Git 安全处理 |
| `global-*.md` | 全局用户画像、风格、工作流及可复用记忆 |
| `global-projects-index.md` | 自动维护的所有已初始化项目和近期会话索引 |

### 项目层 `.assistant/`

每个项目自动创建：

```
.assistant/
  SYSTEM.md          — 工作区级系统规则和安全边界
  USER.md            — 用户画像：姓名、角色、上下文、语言
  STYLE.md           — 沟通风格偏好
  WORKFLOW.md        — 工作方式
  TOOLS.md           — 工具偏好和边界
  MEMORY.md          — 精炼的长期可复用记忆
  BOOTSTRAP.md       — Bootstrap 状态追踪
  sync-policy.md     — 全局记忆同步策略（ask / always / never）
  memory/
    daily/           — 短期日常上下文（YYYY-MM-DD.md）
    projects/        — 项目级跨会话记忆
    sessions/        — 自动归档的会话摘要 + INDEX.md 快速检索
  templates/         — 可复用的模板
  runtime/
    inbox.md         — 短期待办事项
    last-session.md  — 上次会话摘要
    active-task.md   — 当前任务状态 + 执行上下文 + 中断任务队列（统一恢复锚点）
```

最终目的不是"把 Claude Code 变成 OpenClaw"，而是给 Claude Code 增加一层更清晰的外部记忆表面，让它少一点无状态工具感，多一点持续协作感。

## 会话恢复机制

现在的恢复流程围绕单个 `active-task.md` 运转，把”工作区级任务恢复”和”模块级 `PROGRESS.md` 断点”清晰分开：

- `.assistant/runtime/active-task.md` 是唯一的恢复锚点，包含当前任务状态、执行上下文和中断任务队列
- 每次新会话启动时，Claude 会自动检查该文件，如有进行中任务会主动简短提及
- 完成的会话自动归档到 `memory/sessions/`，`INDEX.md` 支持按日期、项目或标签快速检索历史

当用户说 `continue`、`resume`、`刚才做到哪里了`、`恢复刚才的任务` 这类话时，Claude 先读 `active-task.md`，然后输出恢复摘要和可选操作。

## 它改变了什么

- 偏好和协作习惯不再只散落在聊天记录里
- 项目上下文变得可检查、可编辑
- 临时信息和长期信息分层存放
- Claude Code 重新进入工作区时会更有连续性

## 具体例子

1. 表达风格不再每次重讲。  
   例如"保持简洁、先给结论、再讲风险"，可以进入 `.assistant/STYLE.md`。

2. 项目决策不会轻易重置。  
   例如"先不做大重构"的原因，可以进入 `.assistant/memory/projects/architecture.md`。

3. 临时上下文不会污染长期规则。  
   例如某天的未确认信息或阶段性备注，可以进入 `.assistant/memory/daily/YYYY-MM-DD.md`。

4. 工作推进方式可以个性化。  
   例如"先检查、再修改、最后汇报验证"，可以进入 `.assistant/WORKFLOW.md`。

## 它是怎么工作的

这个 starter 采用两层结构：

| 层级 | 路径 | 用途 |
| --- | --- | --- |
| 全局层 | `~/.claude/` | 默认行为、读取顺序、bootstrap 规则、记忆策略 |
| 项目层 | `.assistant/` | 用户画像、风格、工作流、项目记忆、daily 笔记 |

Claude Code 通过读取这套持久化文件结构，减少对短期会话上下文的依赖。

## 仓库内容

- [BOOTSTRAP_PROMPT.md](./BOOTSTRAP_PROMPT.md)  
  面向 Claude Code 的主初始化提示词。

- [RESET_GUIDE.md](./RESET_GUIDE.md)  
  重置与恢复指南。

- [scripts/show-bootstrap.sh](./scripts/show-bootstrap.sh)  
  本地输出 bootstrap 提示词。

- [scripts/show-reset.sh](./scripts/show-reset.sh)
  本地输出 reset 指南。

- [scripts/export-session.sh](./scripts/export-session.sh)
  自动归档 Claude Code 会话摘要（可作为 hook 或手动运行）。

- [README.md](./README.md)
  English documentation.

## 部署方式

1. 克隆或下载这个仓库。
2. 打开 `BOOTSTRAP_PROMPT.md`，或者直接运行：

```bash
./scripts/show-bootstrap.sh
```

3. 在目标工作区里启动 Claude Code。
4. 把提示词一次性发给 Claude Code。
5. 让 Claude Code 完成工作区检查、配置更新，并在需要时进入轻量 bootstrap。

## 包结构

```text
BOOTSTRAP_PROMPT.md
RESET_GUIDE.md
scripts/
  show-bootstrap.sh
  show-reset.sh
  export-session.sh
```

这个仓库现在更像一个 Claude 部署包，而不是一个嵌套在 `skill/` 目录里的提示词文件。

## 快速开始

1. 打开 [BOOTSTRAP_PROMPT.md](./BOOTSTRAP_PROMPT.md)。
2. 复制完整提示词。
3. 在目标工作区里启动 Claude Code。
4. 把提示词一次性发给 Claude Code。
5. 让 Claude Code 完成工作区检查、配置更新，并在需要时进入轻量 bootstrap。

## 这个提示词的定位

这个仓库目前只提供一个主提示词版本，而不是多版本分支。

可以把它理解为一个结构化、完整初始化导向的工作版本：

- 足够主动，能初始化全局层和项目层
- 对文件创建和写入校验有明确要求
- 对记忆结构和 bootstrap 流程有比较清晰的工程立场

## 内置优化

提示词包含 19 项经过实践验证的优化：

1. `@` 导入兼容性检测 + 不支持时自动合并回退
2. 幂等性判断标准（3 行有效内容规则）
3. 写入后逐文件校验
4. daily 日志生命周期（7/14 天清理策略）
5. `last-session.md` 写入时机规则
6. 记忆冲突解决机制
7. Bootstrap 完成状态持久化（`status` 字段）
8. 问题优先级分层（必问 / 推荐 / 自然积累）
9. 用户审计权（查看 / 删除 / 导出 / 月度提醒）
10. `.gitignore` 自动处理
11. 模板系统灵活化（bootstrap 中询问自定义模板）
12. 快速回顾入口（"查看我的配置" / "review my setup"）
13. 工作区确认（防止在非项目目录创建 `.assistant/`）
14. HOME 目录全局模式（直接用 `~/.claude/`，跳过 `.assistant/` 初始化）
15. 全局记忆提炼（项目中发现可复用信息时，支持 4 种同步策略：单次同步 / 仅保留项目 / 默认全部同步 / 默认全部不同步）
16. 模块级 `PROGRESS.md` 断点恢复
17. 主动上下文加载（每次会话启动时自动检查当前任务）
18. 统一 `active-task.md` 作为单一恢复锚点（取代 4 个分散的 runtime 文件）
19. 会话归档与索引检索（`memory/sessions/INDEX.md`）

## 设计原则

- 把全局配置当成默认行为层，而不是永久强控制层
- 用项目本地记忆承载持续演化的上下文
- 不让临时信息污染长期记忆
- 增量编辑比破坏性重写更安全
- 真实 CLI 行为边界，比理想化代理设想更重要

## 重要边界

- `~/.claude/` 更适合被理解为默认行为层，不是永久强制控制层。
- `.assistant/` 是项目本地记忆约定，不是 Claude Code 天然内建协议。
- 最终效果仍然取决于运行时上下文、可用工具和更高优先级指令。

## FAQ

### 为什么不直接只用 `CLAUDE.md`？

因为行为规则和协作记忆不是一回事。

`CLAUDE.md` 更适合承载稳定默认规则；项目历史、用户偏好、临时信息、未完成事项，则更适合进入一个小型记忆结构。

### `.assistant/` 会不会太重？

会，如果设计得过头。

所以这个 starter 尽量把结构控制得比较小。目标不是制造流程负担，而是减少长期工作中的重复磨合。

### 这是不是想取代 Claude Code？

不是。

它只是提示词层面的工作流增强。真正执行任务的仍然是 Claude Code，这个仓库做的是给它补一层更耐用的协作结构。

### 这套方案适合谁？

它更适合那些会在同一个项目里反复使用 Claude Code、希望跨会话保持连续性的人。

如果你只需要偶尔发一个一次性 prompt，不关心本地记忆结构，那它的价值就会小很多。

## 备注

如果 Claude Code 在你的环境里显得过于激进，可以收紧提示词里的执行约束或缩小初始化范围。

如果它过于保守、推进不足，就加强文件创建、校验和 bootstrap 跟进要求。
