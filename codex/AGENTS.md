<!--
  一键安装命令：
    cp ~/env-sync/global/AGENTS.md ~/.codex/AGENTS.md

  或从远程拉取后：
    git clone https://github.com/Samuel86-star/env-sync.git && cp env-sync/global/AGENTS.md ~/.codex/AGENTS.md
-->

# AGENTS.md — Codex 全局行为规范

> 全局技能已安装于 `~/.codex/skills/`，无需在此重复定义。

## 全局技能索引

| 技能 | 作用 | 触发条件 |
|------|------|---------|
| **superpowers** (14 个) | TDD、调试、规划、评审、并行分发、收尾 | 开发全流程 |
| **karpathy-guidelines** | 编码行为四原则 | 每次编码 |
| **caveman** | AI 表达压缩 75% | `/caveman lite/full/ultra` |

## Karpathy 四原则（内嵌）

### 1. Think Before Coding
先思考再编码：陈述假设、展示多种理解、不默然选择。不确定就问。

### 2. Simplicity First
最少代码解决问题。不写推测功能、不为单次使用建抽象。200 行能简化到 50 行就重写。

### 3. Surgical Changes
只动必须动的代码。不改相邻注释/格式/逻辑。匹配现有风格。不改无关死代码。

### 4. Goal-Driven Execution
将模糊任务转可验证目标：「修 bug」→「先写复现测试，再让测试通过」。

## 指令优先级

1. 用户显式指令 — 最高
2. 项目级 AGENTS.md
3. 本全局 AGENTS.md
4. 全局技能
