<div align="center">
  <h1>Claude Code 常用操作指南</h1>
  <p>命令速查、快捷键大全、配置技巧 — 日常使用一站式参考</p>

  [![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
  [![Platform](https://img.shields.io/badge/Platform-Claude%20Code-blue)](https://code.claude.com)
  [![Stars](https://img.shields.io/github/stars/20kiki/claude-code-guide)](https://github.com/20kiki/claude-code-guide)
</div>

---

## 目录

- [斜杠命令](#-斜杠命令)
- [键盘快捷键](#-键盘快捷键)
- [对话中常用操作](#-对话中常用操作)
- [配置指南](#-配置指南)
  - [全局中文回答](#1-全局默认中文回答)
  - [状态栏显示上下文和模型](#2-状态栏显示上下文使用率和当前模型)
- [高效工作流](#-高效工作流)
- [启动选项](#-启动选项)
- [模型选择策略](#-模型选择策略)
- [相关资源](#-相关资源)

---

## ⌨️ 斜杠命令

### 基础对话

| 命令 | 说明 |
|------|------|
| `/help` | 查看所有可用命令 |
| `/clear` | 清空当前对话历史，开始全新任务。**每个新任务前建议执行** |
| `/compact` | 压缩对话上下文，保留关键信息、释放 token 空间。**上下文达 70% 时就主动执行**，不要等自动压缩 |
| `/context` | 查看当前上下文窗口占用百分比和 token 详情 |
| `/cost` | 查看当前会话的 token 消耗和费用估算 |
| `/resume` | 恢复之前的会话，断点续做 |
| `/model` | 切换模型（Sonnet 日常 / Opus 复杂架构 / Haiku 简单任务） |
| `/effort` | 选择思考深度：`low` / `medium` / `high` / `xhigh` / `max` |

### 工作模式

| 命令 | 说明 |
|------|------|
| `/plan` | 进入计划模式（只读），先展示修改方案，审批后才执行。**复杂重构必须先用这个** |
| `/fast` | 切换快速模式，加速响应输出 |
| `/thinking` | 切换深度思考模式 |
| `/btw` | 附带提问，不污染主对话上下文。例如：`/btw 这段重试逻辑是干什么的？` |

### 代码与审查

| 命令 | 说明 |
|------|------|
| `/diff` | 查看当前会话的 git diff，**提交前必看**，审查 AI 改了什么 |
| `/review` | 对当前分支进行代码审查 |
| `/simplify` | 并行审查代码的复用性、质量和效率 |
| `/security-review` | 安全审查当前分支变更 |

### 项目管理

| 命令 | 说明 |
|------|------|
| `/init` | 初始化项目，自动生成 `CLAUDE.md`。**新项目第一步** |
| `/memory` | 编辑 CLAUDE.md 记忆文件。**AI 犯同样错误两次就立即更新** |
| `/todos` | 跨会话持久化任务列表 |

### Git 与版本控制

| 命令 | 说明 |
|------|------|
| `/commit` | 创建 git commit |
| `/commit-push-pr` | 提交 → 推送 → 创建 PR，一步完成 |
| `/clean-gone` | 清理远程已删除的本地分支及关联 worktree |

### 自动化与配置

| 命令 | 说明 |
|------|------|
| `/loop` | 定时循环执行指令。例如：`/loop 5m /review` 每 5 分钟自动审查 |
| `/permissions` | 配置工具的自动审批权限，减少打断 |
| `/statusline` | 配置终端底部状态栏显示内容 |
| `/config` | 修改 Claude Code 配置（主题、模型等） |

### 其他

| 命令 | 说明 |
|------|------|
| `/rename` | 重命名当前会话 |
| `/export` | 导出会话内容 |
| `/agents` | 管理子 Agent，委派专项任务 |

---

## ⌨️ 键盘快捷键

### 必记快捷键

| 快捷键 | 功能 | 使用场景 |
|--------|------|----------|
| `Ctrl + C` | 中断 AI 当前输出或操作 | AI 跑偏时立即停止 |
| `Shift + Tab` | 循环切换模式：普通 → 自动接受 → 计划模式 | 关键文件用计划模式，样板代码用自动模式 |
| `Esc Esc` | 打开回退菜单，可撤销代码修改 | **救命键**，改坏了赶紧按 |
| `Ctrl + R` | 搜索命令历史 | 重复执行之前的指令 |

### 实用快捷键

| 快捷键 | 功能 |
|--------|------|
| `Shift + Enter` | 多行输入，不发送 |
| `Ctrl + T` | 切换任务列表面板 |
| `Ctrl + O` | 切换详细输出模式（显示思考过程） |
| `Tab` | 切换思考过程显示 |
| `Ctrl + D` | 退出 Claude Code |

---

## 💬 对话中常用操作

### `@` 引用文件

精准指定上下文，避免模糊探索：

```
@src/auth.ts              # 引用文件，支持 Tab 自动补全
@src/utils/helper.ts:30   # 引用文件 + 指定行号
```

> 对比：❌ "看看 auth 代码有什么问题" → ✅ "对比 `@src/auth/session.ts:30-90` 和 `@src/api/login.ts:10-60`，解释不匹配的地方"

### `!` 执行 Shell 命令

```
! git status               # 执行 git 命令
! npm run test             # 运行测试
! python script.py         # 运行脚本
```

### `#` 快速写入记忆

```
# 使用 JWT 认证            # 将"使用 JWT 认证"写入 CLAUDE.md
```

### `/btw` 侧问题

```
/btw 这个函数的调用链是什么？
```

问题不会被记入主对话上下文，避免膨胀 token 用量。

---

## 🔧 配置指南

### 1. 全局默认中文回答

让每次新会话都自动使用简体中文，无需重复交代。

**方法一（推荐）：编辑全局 CLAUDE.md**

在 `~/.claude/CLAUDE.md`（Windows 路径：`C:\Users\<用户名>\.claude\CLAUDE.md`）中添加：

```markdown
## 语言偏好
- 始终使用简体中文回答所有问题
- 代码注释和文档也默认使用中文（除非明确要求英文）
- 所有解释、分析、建议均以中文输出
```

**方法二：使用 `/memory` 命令**

切换到用户目录 (`~`) 后启动 Claude Code，然后执行：

```
/memory 请始终使用简体中文回答所有问题
```

> **注意：** `/memory` 在项目目录中执行会写入项目的 `CLAUDE.md`（仅对当前项目生效），在非项目目录执行才会写入全局文件。

---

### 2. 状态栏显示上下文使用率和当前模型

在终端底部实时显示上下文占用百分比，方便在 70% 左右时及时 `/compact`。

**编辑 `~/.claude/settings.json`**，添加 `statusLine` 配置：

```json
{
  "statusLine": {
    "type": "command",
    "command": "python -c \"import sys,json; d=json.load(sys.stdin); p=d['context_window']['used_percentage']; m=d['model']['display_name']; print(f' Context {p}% | {m} ')\""
  }
}
```

**效果：** 终端底部状态栏实时显示：

```
Context 7% | deepseek-v4-pro[1m]
```

**状态栏 JSON 字段说明：**

| 字段路径 | 说明 | 示例值 |
|---------|------|--------|
| `context_window.used_percentage` | 上下文使用百分比 | `7` |
| `context_window.remaining_percentage` | 剩余百分比 | `93` |
| `context_window.context_window_size` | 总窗口大小 (tokens) | `1000000` |
| `model.display_name` | 当前模型显示名 | `deepseek-v4-pro[1m]` |
| `model.id` | 当前模型 ID | `deepseek-v4-pro[1m]` |
| `session_name` | 当前会话名 | `Common Claude Code commands` |
| `cost.total_cost_usd` | 费用估算 (USD) | `1.51` |

**自定义显示内容的示例：**

```json
// 只显示上下文百分比
"command": "python -c \"import sys,json; d=json.load(sys.stdin); print(f'Context {d[\\\"context_window\\\"][\\\"used_percentage\\\"]}%')\""

// 上下文 + 模型 + 会话名
"command": "python -c \"import sys,json; d=json.load(sys.stdin); p=d['context_window']['used_percentage']; m=d['model']['display_name']; n=d['session_name']; print(f' {n} | Context {p}% | {m} ')\""
```

> **注意：** 如果你用的是 DeepSeek 等第三方 API，`cost.total_cost_usd` 是 Claude Code 按 Anthropic 官方定价估算的，并非真实花费。建议不显示费用，直接去第三方后台查看实际账单。

---

## 🚀 高效工作流

### 功能开发流

```
claude → /init → /memory 补充规范 → 描述需求 → /diff 审查改动 → !npm test → /cost → git commit
```

### 调试流

```
claude -c → 粘贴错误信息 → AI 调查（/btw 附带提问）→ /diff → 测试验证 → /compact
```

### 大规模重构流

```
Shift+Tab (切计划模式) → 描述重构目标 → 审查方案 → 确认后执行 → /diff → /simplify → /compact
```

### 日常习惯清单

1. **每个新任务前 `/clear`** — 避免上下文腐烂
2. **维护精炼的 CLAUDE.md** — 省掉每次重复交代
3. **复杂任务先进计划模式** — 先出方案再执行
4. **Sonnet 干活，Opus 攻克难题** — 最高性价比
5. **上下文 70% 就 `/compact`** — 别等自动压缩
6. **提交前跑 `/diff`** — 审查 AI 改了什么
7. **犯错两次就 `/memory`** — 更新规则避免重复踩坑
8. **`@` 精确引用文件** — 不要模糊探索

---

## 📦 启动选项

```bash
claude                        # 当前目录启动
claude -c                     # 继续最近一次会话
claude --resume <id>          # 恢复指定会话
claude --print "问题"         # 一次性查询后退出（适合脚本/CI）
claude --output-format json   # JSON 格式输出
claude update                 # 升级到最新版本
```

---

## 🧠 模型选择策略

| 场景 | 推荐模型 | 原因 |
|------|---------|------|
| 日常编码、功能开发、重构 | **Sonnet** | 速度快、质量好，日常全能 |
| 复杂架构决策、困难调试 | **Opus** | 推理能力强，质量优先 |
| 简单编辑、格式化、查日志 | **Haiku** | 速度最快、成本最低 |

**省钱技巧：** 用 Opus 做计划，用 Sonnet 执行 — 性价比最高的组合。

---

## 📌 主题标签

[`claude-code`](https://github.com/topics/claude-code)
[`developer-tools`](https://github.com/topics/developer-tools)
[`cli`](https://github.com/topics/cli)
[`ai-coding`](https://github.com/topics/ai-coding)
[`productivity`](https://github.com/topics/productivity)
[`cheatsheet`](https://github.com/topics/cheatsheet)
[`chinese`](https://github.com/topics/chinese)

## 📚 相关资源

- [Claude Code 官方文档](https://code.claude.com/docs)
- [Claude Code 高级技巧](https://support.claude.com/en/articles/14554000-claude-code-power-user-tips)
- [Claude Code 会话管理](https://claude.com/blog/using-claude-code-session-management-and-1m-context)
