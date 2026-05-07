# Claude Code 优化配置指南

> 覆盖 Claude Code CLI 和 Claude Code Desktop
> 更新时间：2026-05-07

---

## 目录

1. [配置分层结构](#配置分层结构)
2. [settings.json 详解](#settingsjson-详解)
3. [环境变量配置](#环境变量配置)
4. [Hooks 配置](#hooks-配置)
5. [CLAUDE.md 配置](#cloudemd-配置)
6. [技能（Skills）配置](#技能skills配置)
7. [插件（Plugins）配置](#插件plugins配置)
8. [关键环境变量说明](#关键环境变量说明)
9. [完整配置示例](#完整配置示例)
10. [新电脑快速配置步骤](#新电脑快速配置步骤)

---

## 配置分层结构

Claude Code 从多个层级读取配置，优先级从高到低：

```
1. 项目级 CLAUDE.md       （项目根目录）
2. 全局 CLAUDE.md          （~/.claude/CLAUDE.md）
3. settings.json            （~/.claude/settings.json）
4. 环境变量                  （shell / launchctl）
5. 内置默认值
```

| 配置项 | CLI 读取 | Desktop 读取 |
|--------|:--:|:--:|
| `~/.claude/settings.json` | ✅ | ✅ |
| `~/.claude/CLAUDE.md` | ✅ | ✅ |
| shell 环境变量 (`.zshrc`) | ✅ | ❌ |
| `launchctl setenv` | ❌ | ✅ |
| 项目级 `CLAUDE.md` | ✅ | ✅ |

> **关键**：Desktop 是 GUI 应用，不读 `.zshrc`。Desktop 环境变量需要用 `launchctl setenv`。

---

## settings.json 详解

路径：`~/.claude/settings.json`

### 完整配置项

```json
{
  // ========== 模型配置 ==========
  "model": "opus",                           // 默认模型: opus/sonnet/haiku

  // ========== 环境变量（对话内生效） ==========
  "env": {
    "ANTHROPIC_AUTH_TOKEN": "sk-xxx",        // API Key
    "ANTHROPIC_BASE_URL": "https://...",     // 自定义 API 端点
    "ANTHROPIC_MODEL": "deepseek-v4-flash",  // 覆盖默认模型名
    "ANTHROPIC_REASONING_MODEL": "deepseek-v4-pro",
    "ANTHROPIC_DEFAULT_OPUS_MODEL": "deepseek-v4-pro",
    "ANTHROPIC_DEFAULT_SONNET_MODEL": "deepseek-v4-flash",
    "ANTHROPIC_DEFAULT_HAIKU_MODEL": "Deepseek-chat",
    "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"  // 启用实验性 Agent Teams
  },

  // ========== Hooks ==========
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",                   // 匹配 Bash 工具调用
        "hooks": [
          {
            "type": "command",
            "command": "rtk hook claude"     // 自动用 RTK 压缩命令输出
          }
        ]
      }
    ]
  },

  // ========== UI 配置 ==========
  "statusLine": { ... },                     // 状态栏
  "alwaysThinkingEnabled": true,            // 始终显示思考过程
  "theme": "dark-daltonized",               // 终端主题

  // ========== 插件与市场 ==========
  "enabledPlugins": {
    "codex@openai-codex": true,
    "superpowers@superpowers-dev": true
  },
  "extraKnownMarketplaces": {
    "openai-codex": {
      "source": { "source": "github", "repo": "openai/codex-plugin-cc" }
    },
    "superpowers-dev": {
      "source": { "source": "git", "url": "https://github.com/obra/superpowers.git" }
    }
  }
}
```

### 各配置项说明

| 配置项 | 作用 | 是否必须 |
|--------|------|:--:|
| `model` | 选择模型等级 | 否（有默认）|
| `env.ANTHROPIC_AUTH_TOKEN` | API 认证 | 是 |
| `env.ANTHROPIC_BASE_URL` | 自定义 API 地址（如 DeepSeek 代理） | 使用第三方时必需 |
| `env.ANTHROPIC_MODEL` | 覆盖默认模型名称 | 使用第三方时建议 |
| `env.CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS` | 启用多 Agent 协作 | 推荐 |
| `hooks.PreToolUse` | 工具调用前钩子 | 推荐（配合 RTK）|
| `alwaysThinkingEnabled` | 显示思考过程 | 推荐 |
| `enabledPlugins` | 已启用的插件列表 | 按需 |
| `extraKnownMarketplaces` | 自定义插件源 | 按需 |

---

## 环境变量配置

### CLI 环境变量（`~/.zshrc`）

```bash
# Claude Code 核心
export CLAUDE_CODE_FORK_SUBAGENT=1          # 子 Agent 继承对话上下文

# 如果你的 API 提供商需要
export ANTHROPIC_AUTH_TOKEN="sk-xxx"
export ANTHROPIC_BASE_URL="https://api.deepseek.com/anthropic"
```

> 也可以写在 `~/.claude/.env` 中（如果文件存在会被自动加载）。

### Desktop 环境变量（`launchctl setenv`）

```bash
# Desktop 不读 ~/.zshrc，需要用 launchctl
launchctl setenv CLAUDE_CODE_FORK_SUBAGENT 1

# 验证
launchctl getenv CLAUDE_CODE_FORK_SUBAGENT

# ⚠️ launchctl setenv 重启后丢失，需要持久化
# 方式1: 创建 LaunchAgent plist
# 方式2: 每次启动前执行 setenv（放到开机脚本）
```

### 持久化 Desktop 环境变量（推荐）

创建 `~/Library/LaunchAgents/com.claude-code.env.plist`：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN"
  "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.claude-code.env</string>
    <key>ProgramArguments</key>
    <array>
        <string>/bin/launchctl</string>
        <string>setenv</string>
        <string>CLAUDE_CODE_FORK_SUBAGENT</string>
        <string>1</string>
    </array>
    <key>RunAtLoad</key>
    <true/>
</dict>
</plist>
```

```bash
# 加载生效
launchctl load ~/Library/LaunchAgents/com.claude-code.env.plist
```

---

## Hooks 配置

### RTK Hook（命令输出压缩）

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

**效果**：所有 Bash 命令自动加 `rtk` 前缀，输出被压缩 60-90%，大幅节省 token。

**前提**：已安装 RTK（`brew install rtk`）。

### 支持的 Hook 事件

| 事件 | 触发时机 |
|------|---------|
| `PreToolUse` | 工具调用前 |
| `PostToolUse` | 工具调用后 |
| `SessionStart` | 会话启动时 |
| `Stop` | 会话结束时 |

---

## CLAUDE.md 配置

路径：`~/.claude/CLAUDE.md`

### 推荐内容

```markdown
@RTK.md

# 以下是项目特定的自定义指令...
```

**最佳实践**：
- 用 `@` 引用其他文件（如 `@RTK.md`），保持配置模块化
- 全局 `CLAUDE.md` 只放通用指令
- 项目级 `CLAUDE.md` 放项目特定规则

---

## 技能（Skills）配置

### 推荐安装的技能

| 技能 | 作用 | 安装方式 |
|------|------|---------|
| **caveman** | AI 表达压缩 75% | 手动创建 `SKILL.md` |
| **karpathy-guidelines** | 编码质量四原则 | 克隆仓库或插件安装 |
| **superpowers** | 完整工作流（TDD、调试、规划） | 插件市场 |
| **find-skills** | 技能发现 | 已有 |

### 目录结构

```
~/.agents/skills/                    # 共享技能源
├── caveman/SKILL.md
├── karpathy-guidelines/SKILL.md
├── ...

~/.claude/skills/                     # → 软链接到 ~/.agents/skills/
├── caveman -> ../../.agents/skills/caveman
├── karpathy-guidelines -> ../../.agents/skills/karpathy-guidelines
├── find-skills -> ../../.agents/skills/find-skills
├── ...
```

**优点**：所有工具共享同一份技能源，改一处全部生效。

---

## 插件（Plugins）配置

### 当前已安装

| 插件 | 版本 | 功能 |
|------|------|------|
| `codex@openai-codex` | 1.0.4 | Codex 集成 |
| `superpowers@superpowers-dev` | 5.1.0 | TDD/调试/规划工作流 |

### 安装新插件

```bash
# 从已知市场安装
claude plugins install <plugin>@<marketplace>

# 添加新的市场源
cd ~/.claude
# 编辑 settings.json → extraKnownMarketplaces
# 然后
claude plugins install <plugin>@<new-marketplace>
```

---

## 关键环境变量说明

### `CLAUDE_CODE_FORK_SUBAGENT=1`

| 属性 | 说明 |
|------|------|
| **作用** | 子 Agent 启动时继承当前对话的完整上下文 |
| **不开启时** | 子 Agent 是空白对话，需要在任务中重新描述所有背景 |
| **开启后** | 子 Agent 能看到对话历史，理解任务全貌 |
| **推荐值** | `1`（开启） |
| **何时必开** | 使用 Agent Teams、Superpowers 并行分发工作流时 |

**与 `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS` 的关系：**

- `AGENT_TEAMS` 让你能派发多个子 Agent
- `FORK_SUBAGENT` 让子 Agent 继承你的对话上下文
- 两者**互补**，建议同时开启

**是否需要重启？**

- CLI：新开终端窗口即可（重读 `.zshrc`）
- Desktop：需要运行 `launchctl setenv` 后重启 Claude Code Desktop

### `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`

启用实验性的多 Agent 协作功能，允许在对话中派发多个子 Agent 并行工作。

### API 相关环境变量

| 变量 | 说明 |
|------|------|
| `ANTHROPIC_AUTH_TOKEN` | API 密钥 |
| `ANTHROPIC_BASE_URL` | API 端点地址（使用第三方代理时必须设） |
| `ANTHROPIC_MODEL` | 默认模型名称 |
| `ANTHROPIC_SMALL_FAST_MODEL` | 轻量快速模型（Haiku） |
| `ANTHROPIC_REASONING_MODEL` | 推理模型 |

---

## 完整配置示例

### 步骤 1：安装基础工具

```bash
# RTK（命令输出压缩）
brew install rtk

# 验证
rtk --version
```

### 步骤 2：配置 settings.json

```bash
cat > ~/.claude/settings.json << 'SETEOF'
{
  "env": {
    "ANTHROPIC_AUTH_TOKEN": "你的API密钥",
    "ANTHROPIC_BASE_URL": "https://api.deepseek.com/anthropic",
    "ANTHROPIC_MODEL": "deepseek-v4-flash",
    "ANTHROPIC_REASONING_MODEL": "deepseek-v4-pro",
    "ANTHROPIC_DEFAULT_HAIKU_MODEL": "Deepseek-chat",
    "ANTHROPIC_DEFAULT_OPUS_MODEL": "deepseek-v4-pro",
    "ANTHROPIC_DEFAULT_SONNET_MODEL": "deepseek-v4-flash",
    "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
  },
  "model": "opus",
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
  },
  "alwaysThinkingEnabled": true,
  "theme": "dark-daltonized",
  "enabledPlugins": {
    "superpowers@superpowers-dev": true
  },
  "extraKnownMarketplaces": {
    "superpowers-dev": {
      "source": {
        "source": "git",
        "url": "https://github.com/obra/superpowers.git"
      }
    }
  }
}
SETEOF
```

### 步骤 3：配置环境变量

```bash
# CLI
echo 'export CLAUDE_CODE_FORK_SUBAGENT=1' >> ~/.zshrc
source ~/.zshrc

# Desktop
launchctl setenv CLAUDE_CODE_FORK_SUBAGENT 1
```

### 步骤 4：配置 CLAUDE.md

```bash
echo '@RTK.md' > ~/.claude/CLAUDE.md
```

### 步骤 5：创建 RTK.md

```bash
cat > ~/.claude/RTK.md << 'RTKEOF'
# RTK - Rust Token Killer

**Usage**: Token-optimized CLI proxy (60-90% savings on dev operations)

## Hook-Based Usage
All commands automatically rewritten by Claude Code hook.
Example: `git status` → `rtk git status`

## Meta Commands
```bash
rtk gain              # Token savings analytics
rtk gain --history    # Command usage history
rtk proxy <cmd>       # Run raw command (debugging)
```
RTKEOF
```

### 步骤 6：安装技能

```bash
# Caveman
mkdir -p ~/.agents/skills/caveman
# 复制 SKILL.md 到该目录
ln -s ~/.agents/skills/caveman ~/.claude/skills/caveman

# Karpathy Guidelines
git clone https://github.com/forrestchang/andrej-karpathy-skills.git /tmp/ak
cp -r /tmp/ak/skills/karpathy-guidelines ~/.agents/skills/karpathy-guidelines
ln -s ~/.agents/skills/karpathy-guidelines ~/.claude/skills/karpathy-guidelines
rm -rf /tmp/ak

# Superpowers（插件）
claude plugins install superpowers@superpowers-dev
```

### 步骤 7：验证

```bash
# 检查配置
cat ~/.claude/settings.json
cat ~/.claude/CLAUDE.md
ls ~/.claude/skills/

# 检查环境变量
echo $CLAUDE_CODE_FORK_SUBAGENT          # CLI: 应为 1
launchctl getenv CLAUDE_CODE_FORK_SUBAGENT  # Desktop: 应为 1

# 启动 Claude Code 验证
claude
# 说："列出你的可用技能"
```

---

## 新电脑快速配置步骤

```bash
# 1. 安装 RTK
brew install rtk

# 2. 克隆配置仓库
git clone https://github.com/Samuel86-star/env-sync.git /tmp/env-sync

# 3. 复制 settings.json（替换 API Key 后用）
cp /tmp/env-sync/.claude-settings.example.json ~/.claude/settings.json
# ⚠️ 手动编辑，填入你的 API Key

# 4. 配置环境变量
echo 'export CLAUDE_CODE_FORK_SUBAGENT=1' >> ~/.zshrc
source ~/.zshrc
launchctl setenv CLAUDE_CODE_FORK_SUBAGENT 1

# 5. 创建 CLAUDE.md 和 RTK.md
echo '@RTK.md' > ~/.claude/CLAUDE.md
# 复制 RTK.md 内容

# 6. 安装技能（参考文档）
# caveman: install-caveman.md
# karpathy-guidelines: install-karpathy-guidelines.md
# superpowers: claude plugins install superpowers@superpowers-dev

# 7. 重启 Claude Code
claude
```

---

## 参考链接

- [Claude Code 官方文档](https://docs.anthropic.com/en/docs/claude-code)
- [Claude Code 插件文档](https://docs.anthropic.com/en/docs/claude-code/plugins)
- [RTK 官网](https://www.rtk-ai.app/)
- [Superpowers 仓库](https://github.com/obra/superpowers)
- [Karpathy Guidelines 仓库](https://github.com/forrestchang/andrej-karpathy-skills)
