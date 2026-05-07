# gstack 安装与配置指南

> 仓库：https://github.com/garrytan/gstack
> 适用工具：Claude Code / OpenAI Codex / Cursor / OpenCode 等 10+ AI 编码工具
> 作者：Garry Tan（Y Combinator CEO）
> 协议：MIT
> 更新时间：2026-05-07

---

## 目录

1. [简介](#简介)
2. [核心命令速览](#核心命令速览)
3. [Claude Code 安装](#claude-code-安装)
4. [OpenAI Codex 安装](#openai-codex-安装)
5. [Cursor 安装](#cursor-安装)
6. [验证安装](#验证安装)
7. [团队模式](#团队模式)
8. [与你现有技能的配合](#与你现有技能的配合)
9. [常见问题](#常见问题)

---

## 简介

**gstack** 是 Garry Tan 的个人软件开发工厂，将 Claude Code 变成一支虚拟工程团队——CEO 审视产品方向、工程经理锁架构、设计师抓 AI 烂 UI、审查者找生产 bug、QA 测真实浏览器、安全官跑 OWASP+STRIDE 审计、发布工程师 push PR。

23 个角色 + 8 个辅助工具，全部斜杠命令，全部 Markdown，免费 MIT。

**谁适合用：**
- 独立开发者 / 创业者，一个人想搬出团队的速度
- 技术主管 / Staff Engineer，需要每次 PR 都过严审
- 重度 Claude Code 用户，想从「用 AI 写代码」升级到「用 AI 管理工程流程」

---

## 核心命令速览

### 产品决策（CEO 层）

| 命令 | 作用 |
|------|------|
| `/office-hours` | 产品研讨：6 个强制问题深挖需求 |
| `/plan-ceo-review` | CEO 视角审视：挑战范围、10 节评审 |
| `/plan-eng-review` | 工程经理锁架构 |
| `/plan-design-review` | 设计方向评审 |

### 设计层

| 命令 | 作用 |
|------|------|
| `/design-review` | 设计评审（抓 AI slop） |
| `/design-consultation` | 设计咨询 |
| `/design-shotgun` | 快速多方案设计 |
| `/design-html` | 设计稿生成 HTML |

### 质量层

| 命令 | 作用 |
|------|------|
| `/review` | 代码评审 |
| `/qa` | QA 测试（开真实浏览器） |
| `/qa-only` | 仅 QA（不评审代码） |
| `/cso` | 安全审计（OWASP + STRIDE） |
| `/benchmark` | 性能基准测试 |

### 发布层

| 命令 | 作用 |
|------|------|
| `/ship` | 发布 PR |
| `/land-and-deploy` | 合并并部署 |
| `/canary` | 金丝雀发布 |
| `/retro` | 周回顾 |

### 自动化

| 命令 | 作用 |
|------|------|
| `/autoplan` | 自动生成完整实现计划 |
| `/investigate` | 根因调查 |
| `/document-release` | 发布文档 |
| `/browse` | 浏览器操作 |

### 辅助管理

| 命令 | 作用 |
|------|------|
| `/freeze` / `/unfreeze` | 冻结/解冻变更 |
| `/guard` | 防护模式 |
| `/careful` | 谨慎模式 |
| `/gstack-upgrade` | 一键升级 |

---

## Claude Code 安装

### 方法 A：一键安装（推荐）

在 Claude Code 对话中粘贴以下内容，Claude 自动执行：

> Install gstack: run **`git clone --single-branch --depth 1 https://github.com/garrytan/gstack.git ~/.claude/skills/gstack && cd ~/.claude/skills/gstack && ./setup`** then add a "gstack" section to CLAUDE.md that says to use the /browse skill from gstack for all web browsing, never use mcp__claude-in-chrome__* tools, and lists the available skills: /office-hours, /plan-ceo-review, /plan-eng-review, /plan-design-review, /design-consultation, /design-shotgun, /design-html, /review, /ship, /land-and-deploy, /canary, /benchmark, /browse, /connect-chrome, /qa, /qa-only, /design-review, /setup-browser-cookies, /setup-deploy, /setup-gbrain, /retro, /investigate, /document-release, /codex, /cso, /autoplan, /plan-devex-review, /devex-review, /careful, /freeze, /guard, /unfreeze, /gstack-upgrade, /learn. Then ask the user if they also want to add gstack to the current project so teammates get it.

### 方法 B：手动安装

```bash
# 1. 安装前置依赖
brew install bun

# 2. 克隆仓库
git clone --single-branch --depth 1 https://github.com/garrytan/gstack.git ~/.claude/skills/gstack

# 3. 运行 setup
cd ~/.claude/skills/gstack && ./setup

# 4. 验证
ls ~/.claude/skills/gstack-*
```

### 团队模式（推荐）

```bash
(cd ~/.claude/skills/gstack && ./setup --team) && \
  ~/.claude/skills/gstack/bin/gstack-team-init required && \
  git add .claude/ CLAUDE.md && \
  git commit -m "require gstack for AI-assisted work"
```

---

## OpenAI Codex 安装

```bash
# 1. 安装前置依赖
brew install bun

# 2. 克隆并安装
git clone --single-branch --depth 1 https://github.com/garrytan/gstack.git ~/.claude/skills/gstack

# 3. 为 Codex 生成技能
cd ~/.claude/skills/gstack && ./setup --host codex

# 4. 验证
ls ~/.codex/skills/ | grep gstack | wc -l
# 应显示: 46
```

> Codex 生成的是前缀 `gstack-` 的独立技能（如 `gstack-office-hours`），与 Claude Code 的斜杠命令风格不同。
> 重启 Codex 后生效。

---

## Cursor 安装

```bash
# 1. 克隆仓库（同上）
git clone --single-branch --depth 1 https://github.com/garrytan/gstack.git ~/.claude/skills/gstack
cd ~/.claude/skills/gstack && ./setup

# 2. gstack 自动生成 .cursor/skills/ 目录，创建软链接
mkdir -p ~/.cursor/skills
for d in ~/.claude/skills/gstack/.cursor/skills/gstack-*; do
  name=$(basename "$d")
  ln -sfn "$d" ~/.cursor/skills/"$name"
done

# 3. 验证
ls ~/.cursor/skills/ | grep gstack | wc -l
# 应显示: 45+
```

---

## 验证安装

### Claude Code 验证

```bash
# 查看已安装技能
ls ~/.claude/skills/ | grep gstack | wc -l

# 在 Claude Code 中测试
claude
# 输入：/office-hours
```

### Codex 验证

```bash
ls ~/.codex/skills/ | grep gstack | wc -l
# 应显示: 46
# 重启 Codex 后可用
```

### Cursor 验证

```bash
ls ~/.cursor/skills/ | grep gstack | wc -l
# 重启 Cursor 后可用
```

---

## 与你现有技能的配合

| 场景 | 技能组合 |
|------|---------|
| 需求不清晰 | gstack `/office-hours` → Superpowers brainstorming |
| 编码阶段 | gstack `/autoplan` + Karpathy 四原则约束 |
| 代码审查 | gstack `/review` + Superpowers code-review |
| 调试 bug | gstack `/investigate` + Superpowers systematic-debugging |
| 发布前 | gstack `/cso`（安全） + `/qa`（测试） |
| Token 优化 | RTK（命令压缩） + Caveman（表达压缩） |

> gstack 填补了 Superpowers 缺少的**产品决策层**和**发布部署层**，两者互补不重复。

---

## 常见问题

### Q1: `./setup` 报错 `bun is required`？

```bash
# 安装 bun
curl -fsSL https://bun.sh/install | bash
# 或
brew install bun

# 确保 bun 在 PATH 中
export PATH="$HOME/.bun/bin:$PATH"
bun --version
```

### Q2: 安装后提示 `bun install` 权限错误？

```bash
# 清理后重试
rm -rf ~/.claude/skills/gstack/node_modules
cd ~/.claude/skills/gstack && ./setup
```

### Q3: gstack 和 superpowers 会冲突吗？

不会。gstack 侧重**角色分工**（CEO/设计/QA/安全/发布），superpowers 侧重**工程执行**（TDD/调试/并行 agent）。使用场景不同，可以共存。

### Q4: 如何升级 gstack？

```bash
cd ~/.claude/skills/gstack && git pull && ./setup
# 或在 Claude Code 中：/gstack-upgrade
```

### Q5: Codex 中如何使用 gstack？

Codex 没有斜杠命令系统，而是通过技能索引触发。在对话中提及对应的技能名即可，例如：
- "帮我做 CEO 审视这个功能" → 触发 `gstack-plan-ceo-review`
- "审查这段代码" → 触发 `gstack-review`

---

## 附录：配置文件路径速查

```
# gstack 源仓库
~/.claude/skills/gstack/               # 主仓库

# Claude Code 技能（斜杠前缀）
~/.claude/skills/gstack-*/             # 生成到相邻目录
~/.claude/skills/gstack-browse/        # 浏览器技能
~/.claude/skills/gstack-office-hours/  # 产品研讨

# Codex 技能（gstack- 前缀）
~/.codex/skills/gstack-*/              # 46 个技能

# Cursor 技能
~/.cursor/skills/gstack-*/             # 45 个技能（软链接）

# 配置文件
~/.gstack/                             # gstack 全局配置
~/.gstack/sessions/                    # 会话追踪
```

---

## 参考链接

- [gstack 仓库](https://github.com/garrytan/gstack)
- [Garry Tan 的 X](https://x.com/garrytan)
- [关于 LOC 争论](https://github.com/garrytan/gstack/blob/main/docs/ON_THE_LOC_CONTROVERSY.md)
