<div align="center">
  <h1>Claude Code 常用操作指南</h1>
  <p>命令速查、快捷键大全、配置技巧 — 日常使用一站式参考</p>

  [![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
  [![Platform](https://img.shields.io/badge/Platform-Claude%20Code-blue)](https://code.claude.com)
  [![Stars](https://img.shields.io/github/stars/20kiki/claude-code-guide)](https://github.com/20kiki/claude-code-guide)
</div>

---

## 📋 目录

- [斜杠命令](#-斜杠命令)
- [上下文管理三件套](#-上下文管理三件套-rewind--fork--btw)
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

## 📋 斜杠命令

### 基础对话

| 命令 | 说明 |
|------|------|
| `/help` | 查看所有可用命令 |
| `/clear` | 清空当前对话历史，开始全新任务。**每个新任务前建议执行** |
| `/compact` | 压缩对话上下文，保留关键信息、释放 token 空间。**上下文达 70% 时就主动执行**，不要等自动压缩 |
| `/context` | 查看当前上下文窗口占用百分比和 token 详情 |
| `/cost` | 查看当前会话的 token 消耗和费用估算 |
| `/resume` | 恢复之前关闭的会话，回到上次退出的地方继续工作。和 `/rewind` 的区别：`/resume` 是跨会话恢复，`/rewind` 是当前会话内回退 |
| `/model` | 切换模型（Sonnet 日常 / Opus 复杂架构 / Haiku 简单任务） |
| `/effort` | 选择思考深度：`low` / `medium` / `high` / `xhigh` / `max` |
| `/rewind` | 把当前会话回退到之前的某个检查点。三种粒度可选：仅回退代码、仅回退对话、同时回退。也可双击 `Esc Esc` 唤起。别名 `/checkpoint`、`/undo`。**方案跑偏时不需要发消息纠正——直接回退到出错之前，重新描述需求** |
| `/fork` | 从当前对话复制一份到新会话（继承分叉点之前的完整历史），在新会话里自由探索替代方案。**实现到一半想换个思路？分叉出去验证，行不通就丢弃，不影响主线** |

### 工作模式

| 命令 | 说明 |
|------|------|
| `/plan` | 进入计划模式（只读），先展示修改方案，审批后才执行。**复杂重构必须先用这个** |
| `/fast` | 切换快速模式，加速响应输出 |
| `/thinking` | 切换深度思考模式 |
| `/btw` | 旁路提问（by the way），在主任务进行中临时问一个问题。问答内容展示一次即销毁，不会写入主对话历史，不占用上下文空间也不干扰后续输出质量。仅 CLI 可用 |

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
| `/insights` | 分析 Claude Code 使用历史，生成 HTML 交互式统计报告 |
| `/powerup` | 内置互动教程，带你发现不常用但实用的隐藏功能 |
| `/vim` | 启用 Vim 编辑模式，用 Vim 快捷键编辑输入 |
| `/ghost` | 消除 AI 语气，输出像人类手写的内容 |
| `/batch` | 大任务自动拆解到数十上百并行 Agent 执行 |

---

## 🧹 上下文管理三件套：`/rewind` / `/fork` / `/btw`

这三个命令解决同一个核心问题：**避免上下文污染，提高对话质量**。

### 什么是上下文污染

Claude Code 中，你和 AI 的每一轮对话都会被永久写入会话历史。正常的功能讨论是必要的上下文，但以下情况会产生有害的「垃圾内容」：

- 你让 AI 尝试方案 A，发现不对，又发消息纠正让它改用方案 B — 方案 A 的错误尝试永久留在了对话里
- 主任务做到一半，你临时问了一个不相关的问题 — 这段无关问答会一直存在
- 你想试试另一种实现思路，但又不确定是否要放弃当前进度

这些内容会持续占用 token 空间，并在后续每一轮对话中被模型读取，干扰输出质量。**在一次长会话中，纠正性消息和临时提问能吃掉 20-30% 的上下文窗口。**

### 如何避免 — 三种策略

| 策略 | 命令 | 何时使用 |
|------|------|----------|
| **直接删除污染** | `/rewind` | 已经产生了无效上下文，回退到污染发生之前 |
| **隔离到新会话** | `/fork` | 想探索新方向但不确定结果，先分叉出去验证 |
| **不写入历史** | `/btw` | 临时想问个问题，但不想让这个问答留在对话里 |

### `/rewind` — 回退到之前的检查点

**解决的问题：** AI 理解错了需求、试了一个失败的方案、你发了一段模糊的指令导致跑偏——这些错误尝试已经写入了对话历史，如果继续在这个会话里纠正，浪费 token 且降低输出质量。

**怎么做：** 用 `/rewind`（或双击 `Esc Esc`）打开回退菜单，从检查点列表里选择要回退到的位置。Claude 会像什么都没发生过一样，从那个位置重新开始。

**三种回退粒度：**

| 选项 | 效果 |
|------|------|
| 仅回退代码 | 代码被还原到检查点时的状态，对话保留。Claude 仍记得之前聊了什么，知道哪条路走不通 |
| 仅回退对话 | 代码保留，对话历史回滚。适合代码没问题但对话跑偏的情况 |
| 两者同时回退 | 代码和对话都回滚到检查点 |

> ⚠️ `/rewind` 只能回退 Claude 通过文件编辑工具（Write、Edit）写入的代码更改，不能回退通过 Bash 命令（如 `rm -rf`、`mv`）造成的文件操作副作用。

### `/fork` — 分叉出一个独立会话继续探索

**解决的问题：** 实现到一半，突然想到另一个方案可能更好。直接在当前会话尝试有风险——如果新方案不行，历史里又多了无效上下文。如果新方案可行，又要手动把当前改动迁移过去。

**怎么做：** 执行 `/fork`，Claude 会把当前对话从这里分叉出一个新会话。新会话继承分叉点之前的完整历史，之后就是独立的两条线——主会话继续原来的方向，新会话自由验证新想法。确认新方案可行后，回到主会话用更好的指令重新实现。

**典型场景：**
- 重构到一半，想到另一种架构可能更简洁 → `/fork` 去验证
- 不确定该用方案 A 还是方案 B → 主会话做 A，`/fork` 试 B，对比后选优
- 想做一次「大改」但又不想丢掉当前进度 → `/fork` 去大胆改

### `/btw` — 旁路提问，不写入对话历史

**解决的问题：** 在 Claude 执行任务时，突然想到一个临时问题。如果直接在对话里问，这段问答会永久写入会话历史，占用 token 空间并干扰后续输出。如果忍到任务结束再问，可能已经忘了。

**怎么做：** 输入 `/btw` 加上你的问题。Claude 会基于当前上下文回答，但问题和回答只展示一次，绝不写入主对话历史。

```
/btw TypeScript 的 Record 和 Map 有什么区别？
/btw 这个重试逻辑是干什么的？
/btw 当前项目的测试覆盖率是怎么配置的？
```

**工作机制：**
- 复用主会话的 prompt cache，读取当前上下文来回答问题
- 回答显示在终端中，带 `[btw]` 前缀
- 只读操作，不能编辑文件或执行命令
- 单轮问答，不支持追问

> ⚠️ `/btw` 仅限 CLI 终端中使用，VS Code 扩展暂不支持。

---

## ⚡ 键盘快捷键

### 必记快捷键

| 快捷键 | 功能 | 使用场景 |
|--------|------|----------|
| `Ctrl + C` | 中断 AI 当前输出或操作 | AI 跑偏时立即停止 |
| `Shift + Tab` | 循环切换模式：普通 → 自动接受 → 计划模式 | 关键文件用计划模式，样板代码用自动模式 |
| `Esc Esc` | 打开回退菜单，可撤销代码修改 | **救命键**，改坏了赶紧按 |
| `Ctrl + R` | 搜索命令历史 | 重复执行之前的指令 |
| `Ctrl + B` | 当前任务转入后台运行 | 任务耗时较长时切到后台，不阻塞输入 |

### 实用快捷键

| 快捷键 | 功能 |
|--------|------|
| `Shift + Enter` | 多行输入，不发送 |
| `Ctrl + T` | 切换任务列表面板 |
| `Ctrl + O` | 切换详细输出模式（显示思考过程） |
| `Tab` | 切换思考过程显示 |
| `Ctrl + D` | 退出 Claude Code |
| `Shift + Up` | 消息选择器，可复制消息内容 |
| `Ctrl + Shift + F` | 全局文件搜索 |

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

### `/btw` 旁路提问

主任务进行中临时想问一个问题，但又不想这段问答永久写入对话历史占用 token 空间——用 `/btw`：

```
/btw 这个函数的调用链是什么？
/btw TypeScript 中 Record 和 Map 的区别？
```

问答内容只在终端展示一次（带 `[btw]` 前缀），不会写入主对话历史，不占用上下文窗口，也不干扰主任务的输出质量。复用主会话的 prompt cache，成本极低。

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
9. **方案不对就 `/rewind`** — 别发消息纠正，直接回退
10. **临时问题用 `/btw`** — 别污染主对话上下文

### 并行开发流

```bash
# 终端 1：主力开发
cd project && claude

# 终端 2：同步跑 Code Review
cd project && claude -w

# 终端 3：同步研究技术方案
cd project && claude -w --tmux
```

> 💡 设别名快速切换：`alias za='cd /path/to/worktree-a'`, `alias zb='cd /path/to/worktree-b'`

### 后台自动化流

```bash
/loop 5m /babysit            # 每 5 分钟：自动 review、rebase、推进 PR
/loop 30m /slack-feedback    # 每 30 分钟：检查 Slack 反馈并创建 PR
/loop 1h /pr-pruner          # 每小时：自动关闭陈旧 PR
```

---

## 📦 启动选项

### 会话管理

```bash
claude                        # 当前目录启动
claude -c                     # 继续最近一次会话（最常用，相当于断点续做）
claude --resume <id>          # 恢复指定会话
claude --resume               # 交互式选择要恢复的会话
claude -r <id>                # --resume 的简写
```

### 并行工作与隔离

```bash
claude -w                     # 创建独立 worktree，并行跑多个任务互不干扰
claude --worktree             # 等同于 -w
claude --tmux                 # 在独立 tmux 窗口中启动
```

> 💡 **并行 Worktree 是最高效的生产力工具**：同时启动 3-5 个 Claude 会话，各自在不同 git worktree 中独立工作，互不阻塞。设别名 `za`, `zb`, `zc` 快速跳转。

### 输出与自动化

```bash
claude --print "问题"          # 一次性查询后退出（适合脚本/CI）
claude -p "问题"               # --print 的简写
claude --output-format json    # JSON 格式输出
claude --output-format stream-json  # 流式 JSON 输出
claude --max-turns N           # 限制最大对话轮次
```

### 调试与诊断

```bash
claude --debug-file debug.log  # 输出调试日志到文件
claude --debug                 # 启用调试模式（等价环境变量 CLAUDE_CODE_DEBUG=1）
claude -d                      # 同 --debug
claude --bare                  # 极简 SDK 模式，启动快 10 倍（仅消息 API，无工具/CLI）
```

### 自动化与权限

```bash
claude --enable-auto-mode      # 启用自动模式，分类器自动判断安全操作，减少询问
claude --permission-prompt-tool ""  # 跳过权限提示（用于 CI）
```

### 升级

```bash
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
