# RTK + Caveman 安装与配置指南

> 适用工具：Claude Code / OpenAI Codex / Cursor（部分）
> 更新时间：2026-05-07

---

## 目录

1. [RTK 安装与配置](#rtk-安装与配置)
2. [Caveman 安装与配置](#caveman-安装与配置)
3. [验证步骤](#验证步骤)
4. [使用说明](#使用说明)
5. [工具兼容性总结](#工具兼容性总结)
6. [常见问题](#常见问题)

---

## RTK 安装与配置

### 什么是 RTK

**RTK (Rust Token Killer)** 是一个 CLI 代理工具，用于在 AI 工具执行 shell 命令时自动压缩输出，节省 **60-90%** 的 token 消耗。

### 安装

```bash
# macOS（通过 Homebrew）
brew install rtk

# 验证安装
rtk --version        # 应显示: rtk 0.38.0
which rtk            # 应显示: /opt/homebrew/bin/rtk
```

> Linux 用户可以从 [GitHub Releases](https://github.com/reachingforthejack/rtk/releases) 下载对应平台的二进制文件，或从源码编译。

### 配置 Claude Code（桌面版 + CLI 版统一）

编辑 `~/.claude/settings.json`，添加 RTK hook：

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "rtk hook claude"
          }
        ]
      }
    ]
  }
}
```

创建 `~/.claude/RTK.md`：

```markdown
# RTK - Rust Token Killer

**Usage**: Token-optimized CLI proxy (60-90% savings on dev operations)

## Meta Commands (always use rtk directly)

```bash
rtk gain              # Show token savings analytics
rtk gain --history    # Show command usage history with savings
rtk discover          # Analyze Claude Code history for missed opportunities
rtk proxy <cmd>       # Execute raw command without filtering (for debugging)
```

## Installation Verification

```bash
rtk --version         # Should show: rtk X.Y.Z
rtk gain              # Should work (not "command not found")
which rtk             # Verify correct binary
```

⚠️ **Name collision**: If `rtk gain` fails, you may have reachingforthejack/rtk (Rust Type Kit) installed instead.

## Hook-Based Usage

All other commands are automatically rewritten by the Claude Code hook.
Example: `git status` → `rtk git status` (transparent, 0 tokens overhead)
```

### 配置 OpenAI Codex（桌面版 + CLI 版统一）

创建 `~/.codex/RTK.md`：

```markdown
# RTK - Rust Token Killer (Codex CLI)

**Usage**: Token-optimized CLI proxy for shell commands.

## Rule

Always prefix shell commands with `rtk`.

Examples:

```bash
rtk git status
rtk cargo test
rtk npm run build
rtk pytest -q
```

## Meta Commands

```bash
rtk gain            # Token savings analytics
rtk gain --history  # Recent command savings history
rtk proxy <cmd>     # Run raw command without filtering
```

## Verification

```bash
rtk --version
rtk gain
which rtk
```
```

> Codex 没有内置 hook 系统，`RTK.md` 通过系统提示让 AI 自动加 `rtk` 前缀，效果不如 Claude Code 的 hook 彻底，但仍有效。

### 配置 Cursor

Cursor 没有直接的 hook 系统，但可以：

1. 在项目根目录的 `.cursorrules` 中添加：
   ```
   执行所有 shell 命令时，在前面加上 `rtk` 前缀以优化 token 使用。
   ```

2. 或使用 `rtk proxy` 模式手动执行：
   ```bash
   rtk proxy <your-command>
   ```

---

## Caveman 安装与配置

### 什么是 Caveman

**Caveman** 是一个 AI 技能，让 AI 以极简风格交流，削减约 **75%** 的 token 消耗，同时保持技术准确性。

支持 6 种强度等级：`lite` / `full` / `ultra` / `wenyan-lite` / `wenyan-full` / `wenyan-ultra`

### 安装

#### 步骤 1：创建技能文件

```bash
# 创建共享技能目录
mkdir -p ~/.agents/skills/caveman
```

#### 步骤 2：写入 SKILL.md

```bash
cat << 'EOF' > ~/.agents/skills/caveman/SKILL.md
---
name: caveman
description: >
  Ultra-compressed communication mode. Cuts token usage ~75% by speaking like caveman
  while keeping full technical accuracy. Supports intensity levels: lite, full (default), ultra,
  wenyan-lite, wenyan-full, wenyan-ultra.
  Use when user says "caveman mode", "talk like caveman", "use caveman", "less tokens",
  "be brief", or invokes /caveman. Also auto-triggers when token efficiency is requested.
---

Respond terse like smart caveman. All technical substance stay. Only fluff die.

## Persistence

ACTIVE EVERY RESPONSE. No revert after many turns. No filler drift. Still active if unsure. Off only: "stop caveman" / "normal mode".

Default: **full**. Switch: `/caveman lite|full|ultra`.

## Rules

Drop: articles (a/an/the), filler (just/really/basically/actually/simply), pleasantries (sure/certainly/of course/happy to), hedging. Fragments OK. Short synonyms (big not extensive, fix not "implement a solution for"). Technical terms exact. Code blocks unchanged. Errors quoted exact.

Pattern: `[thing] [action] [reason]. [next step].`

Not: "Sure! I'd be happy to help you with that. The issue you're experiencing is likely caused by..."
Yes: "Bug in auth middleware. Token expiry check use `<` not `<=`. Fix:"

## Intensity

| Level | What change |
|-------|------------|
| **lite** | No filler/hedging. Keep articles + full sentences. Professional but tight |
| **full** | Drop articles, fragments OK, short synonyms. Classic caveman |
| **ultra** | Abbreviate prose words (DB/auth/config/req/res/fn/impl), strip conjunctions, arrows for causality (X → Y), one word when one word enough. Code symbols, function names, API names, error strings: never abbreviate |
| **wenyan-lite** | Semi-classical. Drop filler/hedging but keep grammar structure, classical register |
| **wenyan-full** | Maximum classical terseness. Fully 文言文. 80-90% character reduction. Classical sentence patterns, verbs precede objects, subjects often omitted, classical particles (之/乃/為/其) |
| **wenyan-ultra** | Extreme abbreviation while keeping classical Chinese feel. Maximum compression, ultra terse |

Example — "Why React component re-render?"
- lite: "Your component re-renders because you create a new object reference each render. Wrap it in `useMemo`."
- full: "New object ref each render. Inline object prop = new ref = re-render. Wrap in `useMemo`."
- ultra: "Inline obj prop → new ref → re-render. `useMemo`."
- wenyan-lite: "組件頻重繪，以每繪新生對象參照故。以 useMemo 包之。"
- wenyan-full: "物出新參照，致重繪。useMemo .Wrap之。"
- wenyan-ultra: "新參照→重繪。useMemo Wrap。"

## Auto-Clarity

Drop caveman when:
- Security warnings
- Irreversible action confirmations
- Multi-step sequences where fragment order or omitted conjunctions risk misread
- Compression itself creates technical ambiguity
- User asks to clarify or repeats question

Resume caveman after clear part done.

## Boundaries

Code/commits/PRs: write normal. "stop caveman" or "normal mode": revert. Level persist until changed or session end.
EOF
```

#### 步骤 3：配置 Claude Code（桌面版 + CLI 版统一）

```bash
# 创建软链接
ln -s ~/.agents/skills/caveman ~/.claude/skills/caveman
```

#### 步骤 4：配置 OpenAI Codex（桌面版 + CLI 版统一）

```bash
# 复制技能到 Codex skills 目录
# （注意：Codex 不支持软链接，需要复制目录）
cp -r ~/.agents/skills/caveman ~/.codex/skills/caveman
```

> 验证：`ls -la ~/.codex/skills/` 应能看到 `caveman` 目录。

#### 步骤 5（可选）：Cursor

Cursor 没有技能系统，在 `.cursorrules` 中添加：
```
可以使用 caveman mode 减少 token 消耗。说 "caveman lite/full/ultra" 切换。
```

---

## 验证步骤

### RTK 验证

```bash
# 1. 检查版本
rtk --version
# 期望输出: rtk 0.38.0

# 2. 检查 savings 统计
rtk gain
# 期望: 显示 token 节省统计

# 3. 测试 hook 是否生效（在 Claude Code 中执行一个 bash 命令）
# 观察命令是否被自动重写为 `rtk <cmd>`
```

### Caveman 验证

```bash
# 1. 检查 Claude Code 技能文件
ls -la ~/.claude/skills/caveman

# 2. 检查 Codex 技能文件
ls -la ~/.codex/skills/caveman

# 3. 在 Claude Code 或 Codex 中测试
# 启动对话后说：
# "/caveman lite"
# AI 回复应该变得简洁（省略填充词但保留完整句子结构）
```

---

## 使用说明

### RTK 日常使用

| 场景 | 命令 |
|------|------|
| 查看节省统计 | `rtk gain` |
| 查看历史 | `rtk gain --history` |
| 绕过过滤（调试）| `rtk proxy <cmd>` |
| Claude Code 自动代理 | 无需操作，hook 自动处理 |
| Codex 手动前缀 | `rtk git status` 等 |

### Caveman 日常使用

| 指令 | 效果 |
|------|------|
| `/caveman lite` | 轻度压缩，保留完整句子 |
| `/caveman full` | 经典 caveman，省略冠词 |
| `/caveman ultra` | 最大压缩，缩写常用词 |
| `/caveman wenyan-full` | 文言文模式 |
| `stop caveman` / `normal mode` | 恢复正常 |

---

## 工具兼容性总结

| 功能 | Claude Code Desktop | Claude Code CLI | Codex Desktop | Codex CLI | Cursor |
|------|:--:|:--:|:--:|:--:|:--:|
| RTK hook | ✅ 自动 | ✅ 自动 | ⚠️ RTK.md | ⚠️ RTK.md | ❌ |
| RTK 手动 | ✅ | ✅ | ✅ | ✅ | ✅ |
| Caveman skill | ✅ | ✅ | ✅ | ✅ | ❌ |
| 配置路径 | `~/.claude/` | `~/.claude/` | `~/.codex/` | `~/.codex/` | 项目目录 |

> ✅ = 完整支持　⚠️ = 部分支持（需手动触发）　❌ = 不支持

> **关键点**：Claude Code 和 Codex 的**桌面版与 CLI 版共享同一套 `~/.claude/` / `~/.codex/` 配置目录**，只需配置一次即可。

---

## 常见问题

### Q1: `brew install rtk` 失败？

```bash
# 先更新 Homebrew
brew update

# 如果还是失败，从源码编译
brew install --build-from-source rtk

# 或直接从 GitHub 下载 release
curl -L https://github.com/reachingforthejack/rtk/releases/latest/download/rtk-$(uname -m)-apple-darwin.tar.gz | tar xz
sudo mv rtk /usr/local/bin/
```

### Q2: RTK hook 没有生效？

检查 Claude Code 的 `settings.json`：
1. JSON 格式是否正确（逗号、括号）
2. `hooks.PreToolUse` 路径是否正确
3. 重启 Claude Code

### Q3: Caveman 指令 AI 不理解？

确保 `SKILL.md` 已正确写入 `~/.agents/skills/caveman/SKILL.md`，且对应的软链接/复制目录存在。

如果 AI 不认识 `/caveman`，可以直接说：
```
请用 caveman mode 回复我，省略冠词和填充词，保持技术准确。
```

### Q4: `rtk gain` 提示 command not found？

可能是与 `reachingforthejack/rtk` (Rust Type Kit) 命名冲突。确认安装的是 `rtk-ai` 版本：
```bash
which -a rtk
rtk --version
```

### Q5: Codex 的 caveman 不生效？

Codex 不支持软链接，必须用 `cp -r` 复制整个目录。确保：
```bash
ls ~/.codex/skills/caveman/SKILL.md
# 文件必须存在
```

然后**重启 Codex**，新对话中即可使用 `/caveman`。

---

## 附录：配置文件路径速查

```
# RTK
/opt/homebrew/bin/rtk                # macOS Homebrew 安装路径
~/.claude/settings.json              # Claude Code RTK hook
~/.claude/RTK.md                     # Claude Code RTK 文档
~/.codex/RTK.md                      # Codex RTK 文档

# Caveman
~/.agents/skills/caveman/SKILL.md    # Caveman 技能定义（源文件）
~/.claude/skills/caveman             # → 软链接到源文件（Claude Code）
~/.codex/skills/caveman              # → 复制自源文件（Codex）
~/.cursorrules                       # Cursor 规则文件（可选）
```

---

## 参考链接

- [RTK 官网](https://www.rtk-ai.app/)
- [RTK GitHub](https://github.com/reachingforthejack/rtk)
- [Homebrew Formula](https://formulae.brew.sh/formula/rtk)
