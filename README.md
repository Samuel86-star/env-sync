# env-sync

AI 工具配置同步仓库，支持 Claude Code / Codex / Cursor 在多台电脑间复用安装文档和配置。

## 目录

```
env-sync/
├── AGENTS.md               # 项目级简说明
├── claude/
│   └── optimization.md     # Claude Code 优化配置
├── cli-proxy/
│   └── install-rtk-and-caveman.md   # RTK + Caveman 安装与配置
├── skills/
│   ├── install-karpathy-guidelines.md    # Karpathy Guidelines 安装
│   └── superpowers-install-guide.md      # Superpowers 安装指南
└── global/
    └── AGENTS.md           # Codex 全局行为规范（复制到 ~/.codex/AGENTS.md）
```

## 快速开始（新电脑）

从远程拉取后按顺序执行：

### 1. 基础工具
```bash
brew install rtk         # 命令输出压缩
```

### 2. 全局行为规范 (Codex)
```bash
cp global/AGENTS.md ~/.codex/AGENTS.md
```
> Codex 重启后生效。

### 3. 安装技能
按文档提示逐个安装：
- `skills/install-karpathy-guidelines.md`
- `skills/superpowers-install-guide.md`

### 4. Claude Code 配置
按 `claude/optimization.md` 和 `cli-proxy/install-rtk-and-caveman.md` 配置。

### 5. Cursor 配置
技能安装文档中均包含 Cursor 对应章节，按需配置。

## 行为规范来源

| 级别 | 文件 |
|------|------|
| Codex 全局 | `global/AGENTS.md` → `~/.codex/AGENTS.md` |
| Codex 技能 | `~/.codex/skills/` (superpowers, karpathy-guidelines, caveman) |
| Claude Code 全局 | `~/.claude/settings.json` + `~/.claude/CLAUDE.md` |
| 本项目级别 | `AGENTS.md` (仓库用途说明) |

## 维护

- 新增文档放在对应目录（`claude/`、`cli-proxy/`、`skills/`、`global/`）
- 全局配置变更需同步更新 `global/AGENTS.md`
- 所有变更提交到 `main` 分支后自动同步到远程
