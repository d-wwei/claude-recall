# Claude Code Companion Starter

[English](./README.md)

把 Claude Code 改造成稳定、有工作区感知、适合长期协作的个人助理——而不是一次性问答工具。

这是一个一键 bootstrap 提示词，让 Claude Code：

- 把持久化的全局规则写入 `~/.claude/`
- 在 `.assistant/` 中初始化结构化的项目记忆
- 通过轻量对话自动收集你的偏好
- 让以后每次进入工作区都有连续性，而不是从零开始

**只需执行一次。** 全局规则写入后永久生效——以后进入任何新项目，Claude Code 会自动初始化并引导。

## 快速开始

1. 打开 [`claude-code-companion-bootstrap.md`](./claude-code-companion-bootstrap.md)
2. 复制其中完整的 `text` 代码块（约 210 行）
3. 在目标工作区里发给 Claude Code
4. Claude Code 会：
   - 在 `~/.claude/` 写入全局助理规则（自动检测 `@` 导入兼容性）
   - 创建 `.assistant/` 项目记忆结构
   - 启动 bootstrap 对话（称呼、角色、风格偏好）

> **只需一次。** 全局规则写入后永久生效——以后每个新项目都会自动初始化，不用再发提示词。

## 提示词做了什么

### 全局层 `~/.claude/`

| 文件 | 作用 |
| --- | --- |
| `CLAUDE.md` | 入口文件，`@` 导入其他规则（不支持时自动合并写入） |
| `assistant-core.md` | 角色行为、读取顺序、冲突解决、快速回顾入口 |
| `bootstrap-rules.md` | 冷启动规则：分层提问、状态追踪、完成持久化 |
| `memory-policy.md` | 记忆写入 / 冲突 / 清理 / 审计规则 |
| `project-filesystem.md` | 项目文件结构定义、Git 安全处理 |

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
  memory/
    daily/           — 短期日常上下文（YYYY-MM-DD.md）
    projects/        — 项目级跨会话记忆
  templates/         — 可复用的模板
  runtime/
    inbox.md         — 短期待办事项
    last-session.md  — 上次会话摘要
```

## 核心理念：文件系统即外部记忆

借鉴 [OpenClaw 的记忆模型](https://docs.openclaw.ai/concepts/memory)，这个提示词把文件系统用作持久化的协作层。

| 层级 | 路径 | 用途 |
| --- | --- | --- |
| 全局层 | `~/.claude/` | 到处生效的行为规则：角色、读取顺序、冲突解决、记忆策略 |
| 项目层 | `.assistant/` | 项目级记忆：用户画像、风格、工作流、决策、日常上下文 |

### 为什么重要

没有结构化记忆，你每次都要重讲：
- 你希望它怎么组织答案
- 你在这个工作区里是什么角色
- 这个项目之前已经做过哪些取舍
- 上一次停在什么位置

有了 `.assistant/`，这些变成可编辑的项目资产，而不是容易散失的聊天残留。

## 内置优化

提示词包含 13 项经过实践验证的优化：

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

## 设计原则

- 把全局配置当成默认偏好层，而不是强控制层
- 优先增量编辑，而不是破坏性重写
- 强调项目本地记忆，而不是假设全局"全知"
- 用轻量 bootstrap 代替冗长问卷
- 尽量贴近 Claude Code 的真实行为边界

## FAQ

### 为什么不直接只用 `CLAUDE.md`？

`CLAUDE.md` 适合放行为规则，但真实协作还有不断演化的偏好、项目历史、临时上下文。用一个小型记忆结构把它们分层存放，更清晰也更好维护。

### `.assistant/` 会不会让工作区变得太重？

不会，如果控制得当。这套提示词刻意把结构保持很小，强调按需、增量补充。目标不是制造流程负担，而是用尽量少的本地记忆减少重复磨合。

### 这套方案适合谁？

适合在同一个项目里反复使用 Claude Code、希望跨会话保持连续性的人。如果你只是偶尔发几个一次性 prompt，这套结构就不太必要。

## 许可证

[MIT](./LICENSE)
