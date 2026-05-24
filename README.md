**Language:** [English](README.md) | [简体中文](zh-CN/README.md)

<div align="center">
  <h1>Claude Code Quick Reference</h1>
  <p>Slash commands, keyboard shortcuts, configuration tips — your daily driver cheatsheet</p>

  [![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
  [![Platform](https://img.shields.io/badge/Platform-Claude%20Code-blue)](https://code.claude.com)
  [![Stars](https://img.shields.io/github/stars/20kiki/claude-code-guide)](https://github.com/20kiki/claude-code-guide)
</div>

---

## 📋 Table of Contents

- [Slash Commands](#-slash-commands)
- [Context Management Trio](#-context-management-trio)
- [Keyboard Shortcuts](#-keyboard-shortcuts)
- [In-Chat Operations](#-in-chat-operations)
- [Configuration Guide](#-configuration-guide)
  - [Default Chinese Responses](#1-global-default-chinese-responses)
  - [Status Bar — Context & Model](#2-status-bar--context-usage--current-model)
- [Efficient Workflows](#-efficient-workflows)
- [Launch Options](#-launch-options)
- [Model Selection Strategy](#-model-selection-strategy)
- [Resources](#-resources)

---

## 📋 Slash Commands

### Basic Chat

| Command | Description |
|---------|-------------|
| `/help` | List all available commands |
| `/clear` | Clear current conversation history, start fresh. **Run before each new task** |
| `/compact` | Compress conversation context — keep key info, free token space. **Actively run at 50% context usage** — output quality noticeably degrades beyond that. Don't wait for auto-compact (triggers at ~95%, when the model is already at its worst) |
| `/resume` | Restore a previously closed session, pick up where you left off. Unlike `/rewind`, this is cross-session recovery |
| `/rewind` | Roll back the current session to a previous checkpoint. Three granularities: code only, conversation only, or both. Also accessible via double-tap `Esc Esc`. Aliases: `/checkpoint`, `/undo`. **When the AI goes off track, don't send corrective messages — rewind to before the mistake and restate your intent** |
| `/fork` | Duplicate the current conversation into a new session (inherits full history up to the fork point). Explore alternatives freely in the new session. **Forked session is fully independent — use /resume in the fork to switch back to the main session** |
| `/context` | View context window usage percentage and token details |
| `/cost` | View token consumption and cost estimate for the current session |
| `/model` | Switch model (Sonnet for daily / Opus for complex architecture / Haiku for simple tasks) |
| `/effort` | Set thinking depth: `low` / `medium` / `high` / `xhigh` / `max` |

### Work Modes

| Command | Description |
|---------|-------------|
| `/plan` | Enter plan mode (read-only) — present an implementation plan first, execute only after approval. **Always use this before complex refactoring** |
| `/fast` | Toggle fast mode for faster output |
| `/thinking` | Toggle extended thinking mode |
| `/btw` | By-the-way question — ask a one-off question while the main task is in progress. Answer is shown once then discarded, never written to conversation history. Does not consume context or affect output quality. CLI only |

### Code & Review

| Command | Description |
|---------|-------------|
| `/diff` | View git diff for the current session. **Always check before committing** — review what the AI changed |
| `/review` | Code review the current branch |
| `/simplify` | Parallel review of code reusability, quality, and efficiency |
| `/security-review` | Security review of pending changes on the current branch |

### Project Management

| Command | Description |
|---------|-------------|
| `/init` | Initialize a project, auto-generate `CLAUDE.md`. **First step for any new project** |
| `/memory` | Edit the CLAUDE.md memory file. **Update it the second time the AI makes the same mistake** |

### Git & Version Control

| Command | Description |
|---------|-------------|
| `/commit` | Create a git commit |
| `/commit-push-pr` | Commit → push → create PR, all in one step |
| `/clean-gone` | Clean up local branches whose remotes have been deleted, including associated worktrees |

### Automation & Config

| Command | Description |
|---------|-------------|
| `/loop` | Run a command on a recurring interval. e.g. `/loop 5m /review` auto-reviews every 5 minutes |
| `/permissions` | Configure automatic approval rules for tools to reduce interruptions |
| `/statusline` | Configure the terminal status bar display |
| `/config` | Modify Claude Code settings (theme, model, etc.) |

### Other

| Command | Description |
|---------|-------------|
| `/rename` | Rename the current session |
| `/export` | Export session content |
| `/agents` | Manage sub-agents. View, create, and configure specialized sub-agents (see below) |
| `/insights` | Analyze Claude Code usage history, generate an interactive HTML report |
| `/powerup` | Built-in interactive tutorial that discovers hidden but useful features |

### Sub-Agent Management

When tackling complex tasks, Claude Code automatically spawns sub-agents to handle subtasks in parallel. Each sub-agent has its own context window, so the massive output from exploration doesn't flood the main conversation.

**Running `/agents` lets you:**
- List all currently available sub-agents
- Create custom sub-agents (stored in `.claude/agents/`)
- Assign models, tool permissions, and max conversation turns per sub-agent

**Custom agent example** (`.claude/agents/code-reviewer.md`):

```markdown
---
name: code-reviewer
description: Dedicated code reviewer. Call after writing code to check for security and logic issues.
model: haiku
tools: Read, Grep, Glob, Bash
---

You are a senior code reviewer. On execution:
1. Run git diff to inspect changes
2. Check for security issues, error handling, naming conventions
3. Report findings categorized as Critical / Warning / Suggestion
```

**Why use sub-agents:** Exploring codebases, running tests, and reviewing changes generates huge amounts of output — that stays in the sub-agent's isolated context. Only the compressed conclusions return to the main conversation, saving tokens and keeping the main session clean.

---

## 🧹 Context Management Trio

These three commands solve the same core problem: **avoid context pollution and improve conversation quality**.

### What Is Context Pollution

In Claude Code, every exchange between you and the AI is permanently written to the session history. Normal feature discussions are necessary context, but certain situations create harmful "junk content":

- You ask the AI to try Approach A, realize it's wrong, then send a correction to use Approach B — the failed attempt at A is permanently stuck in the conversation
- Mid-task, you ask an unrelated question — that off-topic Q&A stays forever
- You want to explore an alternative approach but aren't sure whether to abandon current progress

These consume token space and get read by the model on every subsequent turn, degrading output quality. **In a long session, corrective messages and ad-hoc questions can eat 20-30% of the context window.**

### How to Avoid It — Three Strategies

| Strategy | Command | When to Use |
|----------|---------|-------------|
| **Delete the pollution** | `/rewind` | Invalid context has already been generated — rewind to before it happened |
| **Isolate to a new session** | `/fork` | You want to explore a new direction but aren't sure of the outcome. Verify in the fork first, then return via `/resume` |
| **Don't write to history** | `/btw` | You have a temporary question but don't want the Q&A to persist in the conversation |

### `/rewind` — Roll Back to a Checkpoint

**Problem it solves:** The AI misunderstood the requirements, tried a failed approach, or went off track because of a vague instruction — those failed attempts are already in the conversation history. Continuing in this session wastes tokens and reduces output quality.

**How:** Run `/rewind` (or double-tap `Esc Esc`) to open the rollback menu. Pick the checkpoint you want to return to. Claude resumes as if nothing happened.

**Three rollback granularities:**

| Option | Effect |
|--------|--------|
| Code only | Code is restored to checkpoint state, conversation is kept. Claude still remembers what was discussed and which approach didn't work |
| Conversation only | Code is kept, conversation history is rolled back. Use when the code is fine but the conversation went off track |
| Both | Code and conversation are both rolled back to the checkpoint |

> ⚠️ `/rewind` only rolls back code changes made through Claude's file editing tools (Write, Edit). It cannot undo filesystem side effects from Bash commands (like `rm -rf`, `mv`).

### `/fork` — Fork Off an Independent Session

**Problem it solves:** Mid-implementation, you think of a potentially better approach. Trying it directly in the current session is risky — if it fails, you've polluted the history. If it works, you still have to manually port over your current changes.

**How:** Run `/fork`. Claude duplicates the conversation at the fork point into a new session. The new session inherits the full history before the fork point. After that, it's two independent threads — the main session continues on the original path, the fork explores the new idea freely. If the new approach works, go back to the main session and re-implement with better instructions.

**How to return to the main session:** `/fork` creates a completely independent Claude Code session. Once you're done verifying, enter `/resume` in the forked session, select the main session from the list, and switch back. The fork and main session don't affect each other — changes in the fork won't appear in the main session unless manually ported.

**Typical scenarios:**
- Mid-refactor, think of a cleaner architecture → `/fork` to verify. If it doesn't work, `Ctrl+D` back
- Unsure between Approach A or B → main session does A, `/fork` tries B, compare and pick the winner
- Want to attempt a "big rewrite" without losing current progress → `/fork` and go wild

### `/btw` — Ask Without Writing to History

**Problem it solves:** During a Claude task, a temporary question pops up. Asking it directly permanently writes the Q&A into session history, consuming tokens and affecting future output. Holding the question until the task ends risks forgetting it.

**How:** Type `/btw` followed by your question. The answer appears directly in the terminal and ends when done — no exit steps needed. The main task is unaffected and keeps progressing. Questions and answers are shown once, never written to the main conversation history.

```
/btw What's the difference between TypeScript's Record and Map?
/btw What does this retry logic do?
/btw How is test coverage configured in this project?
```

**How it works:**
- Reuses the main session's prompt cache, reading current context to answer
- Answer displays in the terminal with a `[btw]` prefix
- Read-only — cannot edit files or run commands
- Single-turn Q&A, no follow-ups

> ⚠️ `/btw` is CLI-only. Not available in the VS Code extension.

---

## ⚡ Keyboard Shortcuts

### Chat & Modes

| Shortcut | Action |
|----------|--------|
| `Ctrl + C` | Completely cancel current output, discard results. Use when the AI is totally off track |
| `Esc` | Interrupt current output, keep what's already generated. Use when direction is fine but needs tweaking |
| `Shift + Tab` | Cycle through permission modes (default → acceptEdits → plan → auto), see details below |
| `Alt + T` | Toggle extended thinking on/off |
| `Alt + P` | Open model picker |
| `Ctrl + D` | Exit Claude Code |

### Input Editing

| Shortcut | Action |
|----------|--------|
| `\ + Enter` | Insert a newline without sending |
| `Esc Esc` | Double-tap to clear the input box |
| `Ctrl + S` | Stash current input and clear. Auto-restores after sending the next message. Useful for inserting a quick question mid-typing |
| `Ctrl + A` | Jump to beginning of line |
| `Ctrl + E` | Jump to end of line |
| `Ctrl + U` | Delete from cursor to beginning of line |
| `Ctrl + K` | Delete from cursor to end of line |
| `↑ / ↓` | Browse previous / next history message |
| `Alt + B` | Jump backward one word |
| `Alt + F` | Jump forward one word |

### Info

| Shortcut | Action |
|----------|--------|
| `Ctrl + T` | Toggle task list panel |
| `Ctrl + O` | Open full conversation transcript, view all historical messages |
| `Ctrl + R` | Search command history upwards |
| `?` (English question mark) | View all keyboard shortcuts for the current terminal. **Must be ASCII `?`, Chinese `？` won't work** |

### Shift+Tab Permission Modes

Press `Shift+Tab` to cycle through four modes, controlling whether the AI needs your per-item approval when editing files or running commands:

| Mode | Auto-Approval Scope | Best For |
|------|-------------------|----------|
| **default** | Read files only | Getting started, sensitive code, unsure what the AI will do |
| **acceptEdits** | Read + file edits + safe file commands (`mkdir`, `mv`, `cp`, `touch`) | Trust the AI with files, but still confirm shell commands |
| **plan** | Read-only (no writes, no commands) | Exploring unfamiliar codebases, designing before complex refactors |
| **auto** | Everything (with a background safety classifier) | Long-running tasks with minimal interruptions. Requirements: Team/Enterprise plan + Sonnet 4.6+ / Opus 4.6+ |

> 💡 **Recommended strategy:** Use `acceptEdits` for daily development, switch back to `default` for sensitive code, use `plan` before complex refactors. `auto` mode has a safety classifier that blocks `curl \| bash`, force-push to main, mass deletions, and other dangerous operations.

---

## 💬 In-Chat Operations

### `@` — Reference Files

Precisely specify context, avoid vague exploration:

```
@src/auth.ts              # Reference a file, with Tab autocomplete
@src/utils/helper.ts:30   # Reference a file + line number
```

> Comparison: ❌ "check the auth code for issues" → ✅ "Compare `@src/auth/session.ts:30-90` and `@src/api/login.ts:10-60`, explain what doesn't match"

### `!` — Run Shell Commands

```
! git status               # Run a git command
! npm run test             # Run tests
! python script.py         # Run a script
```

### `#` — Quick Memory Write

```
# Use JWT auth              # Writes "Use JWT auth" to CLAUDE.md
```

### `/btw` — By-the-Way Questions

When you have a temporary question mid-task but don't want the Q&A permanently written to conversation history eating up tokens — use `/btw`:

```
/btw What's the call chain for this function?
/btw What's the difference between TypeScript's Record and Map?
```

Q&A content appears in the terminal only once (with a `[btw]` prefix). It is never written to the main conversation history, doesn't consume context, and doesn't affect the main task's output quality. It reuses the main session's prompt cache for minimal cost.

---

## 🔧 Configuration Guide

### 1. Global Default Chinese Responses

Make every new session default to Simplified Chinese, no need to repeat the instruction every time.

**Method 1 (Recommended): Edit the global CLAUDE.md**

Add this to `~/.claude/CLAUDE.md` (Windows: `C:\Users\<username>\.claude\CLAUDE.md`):

```markdown
## Language Preference
- Always respond in Simplified Chinese
- Code comments and documentation also default to Chinese (unless English is explicitly requested)
- All explanations, analysis, and suggestions output in Chinese
```

**Method 2: Use the `/memory` command**

Switch to your home directory (`~`) then launch Claude Code and run:

```
/memory Please always respond in Simplified Chinese
```

> **Note:** `/memory` run in a project directory writes to that project's `CLAUDE.md` (project-scoped). Run it outside any project to write to the global file.

---

### 2. Status Bar — Context Usage & Current Model

Display real-time context usage percentage at the bottom of the terminal, so you know when to `/compact` around 50%.

**Edit `~/.claude/settings.json`**, add a `statusLine` config:

```json
{
  "statusLine": {
    "type": "command",
    "command": "python -c \"import sys,json; d=json.load(sys.stdin); p=d['context_window']['used_percentage']; m=d['model']['display_name']; print(f' Context {p}% | {m} ')\""
  }
}
```

**Result:** The terminal status bar shows:

```
Context 7% | deepseek-v4-pro[1m]
```

**Status bar JSON field reference:**

| Field Path | Description | Example |
|-----------|-------------|---------|
| `context_window.used_percentage` | Context usage percentage | `7` |
| `context_window.remaining_percentage` | Remaining percentage | `93` |
| `context_window.context_window_size` | Total window size (tokens) | `1000000` |
| `model.display_name` | Current model display name | `deepseek-v4-pro[1m]` |
| `model.id` | Current model ID | `deepseek-v4-pro[1m]` |
| `session_name` | Current session name | `Common Claude Code commands` |
| `cost.total_cost_usd` | Estimated cost (USD) | `1.51` |

**Custom display examples:**

```json
// Context percentage only
"command": "python -c \"import sys,json; d=json.load(sys.stdin); print(f'Context {d[\\\"context_window\\\"][\\\"used_percentage\\\"]}%')\""

// Context + model + session name
"command": "python -c \"import sys,json; d=json.load(sys.stdin); p=d['context_window']['used_percentage']; m=d['model']['display_name']; n=d['session_name']; print(f' {n} | Context {p}% | {m} ')\""
```

> **Note:** If you're using a third-party API like DeepSeek, `cost.total_cost_usd` is Claude Code's estimate based on Anthropic's official pricing, not your actual cost. It's recommended to skip cost display and check your actual bill on the provider's dashboard.

---

## 🚀 Efficient Workflows

### Feature Development Flow

```
claude → /init → /memory (add conventions) → describe requirements → /diff (review changes) → !npm test → /cost → git commit
```

### Debugging Flow

```
claude -c → paste error → AI investigates (/btw for side questions) → /diff → test verification → /compact
```

### Large-Scale Refactoring Flow

```
Shift+Tab (switch to plan mode) → describe refactoring goal → review plan → execute after approval → /diff → /simplify → /compact
```

### Daily Habit Checklist

1. **`/clear` before every new task** — prevent context rot
2. **Maintain a lean CLAUDE.md** — save repetitive explanations
3. **Enter plan mode before complex tasks** — design first, then execute
4. **Sonnet for daily work, Opus for hard problems** — best cost-effectiveness
5. **`/compact` at 50% context** — model quality drops past halfway; waiting for auto-compact (~95%) is too late
6. **`/diff` before every commit** — review what the AI changed
7. **`/memory` after making the same mistake twice** — update rules to avoid repeat errors
8. **`@` for precise file references** — don't explore vaguely
9. **`/rewind` when the approach is wrong** — don't send corrective messages, just roll back
10. **`/btw` for temporary questions** — don't pollute the main conversation context

### Parallel Development Flow

```bash
# Terminal 1: primary development
cd project && claude

# Terminal 2: code review in parallel
cd project && claude -w

# Terminal 3: research solutions in parallel
cd project && claude -w --tmux
```

> 💡 Set aliases for quick switching: `alias za='cd /path/to/worktree-a'`, `alias zb='cd /path/to/worktree-b'`

### Background Automation Flow

```bash
/loop 5m /babysit            # Every 5 min: auto review, rebase, advance PRs
/loop 30m /slack-feedback    # Every 30 min: check Slack feedback and create PRs
/loop 1h /pr-pruner          # Every hour: auto-close stale PRs
```

---

## 📦 Launch Options

### Session Management

```bash
claude                        # Start in current directory
claude -c                     # Continue the most recent session (most common — like resuming from a checkpoint)
claude --resume <id>          # Resume a specific session
claude --resume               # Interactively select a session to resume
claude -r <id>                # Shorthand for --resume
```

### Parallel Work & Isolation

```bash
claude -w                     # Create an isolated worktree, run multiple tasks without interference
claude --worktree             # Same as -w
claude --tmux                 # Launch in an independent tmux window
```

> 💡 **Parallel worktrees are the ultimate productivity tool**: launch 3-5 Claude sessions simultaneously, each in its own git worktree, working independently with zero blocking. Set aliases `za`, `zb`, `zc` for quick jumping.

### Output & Automation

```bash
claude --print "question"          # One-shot query then exit (ideal for scripts/CI)
claude -p "question"               # Shorthand for --print
claude --output-format json        # JSON output format
claude --output-format stream-json # Streaming JSON output
claude --max-turns N               # Limit max conversation turns
```

### Debugging & Diagnostics

```bash
claude --debug-file debug.log  # Write debug logs to file
claude --debug                 # Enable debug mode (equivalent env var: CLAUDE_CODE_DEBUG=1)
claude -d                      # Same as --debug
claude --bare                  # Minimal SDK mode, 10x faster startup (message API only, no tools/CLI)
```

### Automation & Permissions

```bash
claude --enable-auto-mode      # Enable auto mode — classifier auto-judges safe operations, fewer prompts
claude --permission-prompt-tool ""  # Skip permission prompts (for CI)
```

### Upgrade

```bash
claude update                 # Upgrade to the latest version
```

---

## 🧠 Model Selection Strategy

| Scenario | Recommended Model | Why |
|----------|-----------------|-----|
| Daily coding, feature dev, refactoring | **Sonnet** | Fast, high-quality, daily workhorse |
| Complex architecture decisions, tough debugging | **Opus** | Superior reasoning, quality-first |
| Simple edits, formatting, log inspection | **Haiku** | Fastest, lowest cost |

**Money-saving tip:** Plan with Opus, execute with Sonnet — the best cost-effectiveness combo.

---

## 📌 Topics

[`claude-code`](https://github.com/topics/claude-code)
[`developer-tools`](https://github.com/topics/developer-tools)
[`cli`](https://github.com/topics/cli)
[`ai-coding`](https://github.com/topics/ai-coding)
[`productivity`](https://github.com/topics/productivity)
[`cheatsheet`](https://github.com/topics/cheatsheet)
[`chinese`](https://github.com/topics/chinese)

## 📚 Resources

- [Claude Code Official Docs](https://code.claude.com/docs)
- [Claude Code Power User Tips](https://support.claude.com/en/articles/14554000-claude-code-power-user-tips)
- [Claude Code Session Management & 1M Context](https://claude.com/blog/using-claude-code-session-management-and-1m-context)

---

## 👤 Author

[@20kiki](https://github.com/20kiki)
