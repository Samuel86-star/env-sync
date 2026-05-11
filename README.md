# env-sync

AI 工具配置同步仓库，用于在多台电脑间复用 Claude Code / Codex / Cursor 的安装文档和配置。

行为规范由全局 `~/.codex/AGENTS.md` 和全局技能统一管理，本项目不定义额外行为规则。

## 目录

```
env-sync/
├── README.md
├── claude/
│   └── optimization.md                  # Claude Code 优化配置
├── cli-proxy/
│   └── install-rtk-and-caveman.md        # RTK + Caveman 安装与配置
├── codex/
│   └── AGENTS.md                         # Codex 全局行为规范（cp 到 ~/.codex/AGENTS.md）
└── skills/
    ├── install-gstack.md                 # gstack 安装指南
    ├── install-karpathy-guidelines.md    # Karpathy Guidelines 安装
    ├── install-ui-ux-pro-max.md            # UI-UX Pro Max 安装
    ├── install-ruflo.md                  # Ruflo 安装指南
    └── superpowers-install-guide.md      # Superpowers 安装指南
```

## 快速开始（新电脑）

```bash
git clone https://github.com/Samuel86-star/env-sync.git && cd env-sync
```

### 1. 基础工具
```bash
brew install rtk       # 命令输出压缩
brew install bun       # gstack 依赖
npm install -g ruflo   # AI Agent 编排平台
```

### 2. 全局行为规范 (Codex)
```bash
cp codex/AGENTS.md ~/.codex/AGENTS.md
```
> 重启 Codex 生效。

### 3. 安装技能
- `skills/install-gstack.md`
- `skills/install-karpathy-guidelines.md`
- `skills/install-ui-ux-pro-max.md`
- `skills/superpowers-install-guide.md`
- `skills/install-ruflo.md`

### 4. Claude Code 配置
- `claude/optimization.md`
- `cli-proxy/install-rtk-and-caveman.md`

### 5. Cursor 配置
技能安装文档中均有 Cursor 章节，按需配置。

## 已安装技能一览

| 工具 | 技能 | 数量 |
|------|------|:--:|
| Claude Code | superpowers | 14 |
| Claude Code | gstack | 45+ |
| Claude Code | karpathy-guidelines | 1 |
| Claude Code | caveman | 1 |
| Claude Code | ruflo | 60+ |
| Claude Code | ui-ux-pro-max | 1 |
| Codex | superpowers | 14 |
| Codex | gstack | 46 |
| Codex | karpathy-guidelines | 1 |
| Codex | caveman | 1 |
| Codex | ruflo | 60+ |
| Codex | ui-ux-pro-max | 1 |
| Cursor | gstack | 45 |
| Cursor | ruflo | 60+ |

## 行为规范来源

| 级别 | 文件 |
|------|------|
| Codex 全局 | `codex/AGENTS.md` → `~/.codex/AGENTS.md` |
| Codex 技能 | `~/.codex/skills/`: superpowers, gstack, karpathy-guidelines, caveman, ui-ux-pro-max |
| Claude Code 全局 | `~/.claude/settings.json` + `~/.claude/CLAUDE.md` |
| 本项目 | README.md（仓库说明，无额外行为规则） |

## 维护

- 新增文档放对应目录（`claude/`、`cli-proxy/`、`codex/`、`skills/`）
- 全局配置变更需同步 `codex/AGENTS.md`
- 提交到 `main` 后自动同步
