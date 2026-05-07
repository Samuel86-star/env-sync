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
├── skills/
│   ├── install-karpathy-guidelines.md    # Karpathy Guidelines 安装
│   └── superpowers-install-guide.md      # Superpowers 安装指南
└── codex/
    └── AGENTS.md                         # Codex 全局行为规范（复制到 ~/.codex/AGENTS.md）
```

## 快速开始（新电脑）

```bash
git clone https://github.com/Samuel86-star/env-sync.git && cd env-sync
```

### 1. 基础工具
```bash
brew install rtk
```

### 2. 全局行为规范 (Codex)
```bash
cp codex/AGENTS.md ~/.codex/AGENTS.md
```
> 重启 Codex 生效。

### 3. 安装技能
- `skills/install-karpathy-guidelines.md`
- `skills/superpowers-install-guide.md`

### 4. Claude Code 配置
- `claude/optimization.md`
- `cli-proxy/install-rtk-and-caveman.md`

### 5. Cursor 配置
技能安装文档中均有 Cursor 章节，按需配置。

## 行为规范来源

| 级别 | 文件 |
|------|------|
| Codex 全局 | `codex/AGENTS.md` → `~/.codex/AGENTS.md` |
| Codex 技能 | `~/.codex/skills/` (superpowers, karpathy-guidelines, caveman) |
| Claude Code 全局 | `~/.claude/settings.json` + `~/.claude/CLAUDE.md` |
| 本项目 | README.md（仓库说明，无额外行为规则） |

## 维护

- 新增文档放对应目录（`claude/`、`cli-proxy/`、`skills/`、`codex/`）
- 全局配置变更需同步 `codex/AGENTS.md`
- 提交到 `main` 后自动同步
