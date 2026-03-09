# Claude Code 个人助理系统 — 重置指南

## 方法一：完全重置（清零重来）

删除全局层和项目层的所有助理文件，回到初始状态。

```bash
# 删除全局层配置（保留 ~/.claude/ 目录本身和非助理文件）
rm -f ~/.claude/CLAUDE.md ~/.claude/assistant-core.md ~/.claude/bootstrap-rules.md \
      ~/.claude/memory-policy.md ~/.claude/project-filesystem.md \
      ~/.claude/global-user.md ~/.claude/global-style.md \
      ~/.claude/global-workflow.md ~/.claude/global-memory.md \
      ~/.claude/global-projects-index.md ~/.claude/sessions-archive.md

# 删除当前项目层
rm -rf .assistant/
```

> [!CAUTION]
> 不要 `rm -rf ~/.claude/`，该目录下可能还有 Claude Code 自身的配置文件（如 `settings.json`）。

重置后重新发送 bootstrap 提示词即可。

---

## 方法二：只重置项目层（保留全局身份）

保留"你是谁"的全局画像，只清空当前项目的记忆。

```bash
rm -rf .assistant/
```

下次进入项目时 Claude 会自动重新初始化 `.assistant/`，并从全局配置继承用户身份。

---

## 方法三：只重新触发 Bootstrap 引导

不删文件，只让 Claude 重新问你一遍引导问题。

```bash
sed -i '' 's/status: completed/status: in_progress/' .assistant/BOOTSTRAP.md
```

---

## 方法四：在 Claude Code 对话中直接重置

直接发送以下指令：

```text
请重置我的记忆系统：删除 ~/.claude/ 下的所有 .md 文件，删除 .assistant/ 目录，然后重新执行初始化。
```

---

## 该选哪个？

| 场景 | 推荐方法 |
|---|---|
| 提示词大幅更新，想干净重来 | 方法一 |
| 换了新项目，想重新配置项目层 | 方法二 |
| 只想重新回答引导问题 | 方法三 |
| 不想开终端 | 方法四 |
