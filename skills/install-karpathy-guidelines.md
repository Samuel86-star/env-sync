# Karpathy Guidelines 安装与配置指南

> 仓库：https://github.com/forrestchang/andrej-karpathy-skills.git
> 适用工具：Claude Code / OpenAI Codex / Cursor
> 更新时间：2026-05-11

---

## 目录

1. [简介](#简介)
2. [12 条原则概览](#12-条原则概览)
3. [适用性说明](#适用性说明)
4. [Claude Code 安装](#claude-code-安装)
5. [OpenAI Codex 安装](#openai-codex-安装)
6. [Cursor 安装](#cursor-安装)
7. [验证安装](#验证安装)
8. [使用注意事项](#使用注意事项)
9. [与其他技能的配合](#与其他技能的配合)

---

## 简介

**Karpathy Guidelines** 是基于 [Andrej Karpathy 的观察](https://x.com/karpathy/status/2015883857489522876)整理的行为准则，用于减少 LLM 编码中的常见错误。本指南在原版 4 条原则基础上扩展至 **12 条**，覆盖从思考方式到工程交付的全链路。

### 解决的痛点

| 问题 | 表现 |
|------|------|
| 擅自假设 | AI 默默做出错误假设，不澄清、不追问 |
| 过度设计 | 100 行能解决的问题写出 1000 行，堆砌抽象 |
| 误伤代码 | 改 A 时顺手改了 B 的注释/格式/逻辑 |
| 目标模糊 | "make it work" 不是可验证的目标 |
| 模型滥用 | 用模型做路由、重试等确定性工作 |
| 预算失控 | 上下文溢出，静默消耗 token |
| 矛盾妥协 | 混合两种冲突模式成 Frankenstein |
| 盲目增删 | 不看调用方和共享工具就加代码 |
| 测试无效 | 测试只验证行为，不验证业务意图 |
| 状态迷失 | 从一个自己都说不清楚的状态继续 |
| 风格污染 | 偷偷引入个人偏好，破坏一致性 |
| 静默失败 | 跳过、绕过、隐藏问题后说"完成了" |

---

## 12 条原则概览

### 1. Think Before Coding（先思考再编码）

**不要假设。不要隐藏困惑。要展示权衡。**

- 明确陈述你的假设。如果不确定，问。
- 如果存在多种解释，展示它们——不要静默选择。
- 如果存在更简单的方法，说出来。坚定地发起挑战。
- 如果有什么不清楚，停下来。点名困惑所在。提问。

### 2. Simplicity First（简洁优先）

**能够解决问题的最少代码。不写推测性代码。**

- 不添加超出需求的功能
- 不为单次使用的代码创建抽象
- 不添加未被要求的「灵活性」或「可配置性」
- 不为不可能发生的场景添加错误处理
- 如果你写了 200 行但它本可以只用 50 行，重写。

自问：**「一个资深工程师会说这是过度设计吗？」** 如果是，简化。

### 3. Surgical Changes（精准修改）

**只动必须动的东西。只清理自己造成的混乱。**

- 不要「改进」相邻代码、注释或格式
- 不要重构没有问题的代码
- 匹配现有代码风格，即使你觉得有更优写法
- 发现无关的死代码？提出来，不要删

自检：**每一个变更行是否都可以追溯到用户的需求？**

### 4. Goal-Driven Execution（目标驱动执行）

**定义成功标准。循环直到验证通过。**

将模糊的任务转化为可验证的目标：
- 「加验证」→「为无效输入写测试，然后让测试通过」
- 「修 bug」→「写一个复现 bug 的测试，然后让测试通过」
- 「重构 X」→「确保重构前后测试都通过」

成功的标准让你可以独立迭代；模糊的目标（「让它能用」）需要持续澄清。

### 5. Model for Judgment, Code for Determinism
（判断用模型，确定性工作用代码）

**用模型做：分类、草拟、摘要、从非结构化文本中提取。**
**不要用模型做：路由、重试、确定性转换。**

如果代码本身就能回答，代码回答。
如果正则、解析器或类型检查就够了，不要调用模型。
把模型调用留给真正需要判断、语感、自然语言理解的任务。

### 6. Token Budget Is Law（Token 预算是铁律）

**单任务：4,000 token。单会话：30,000 token。**

接近预算时：
- 总结进度，保存状态，重新开始
- 主动暴露超预算，不要静默溢出

预算意识防止上下文崩溃，强迫聚焦。

### 7. Surface Conflicts, Don't Compromise（暴露冲突，不做折中）

**如果两种模式矛盾，选一个（更新的 / 测试更全的）。**

说明原因，把另一个标记为待清理。
不要混用矛盾的模式拼成 Frankenstein 方案。

如果冲突是架构级的且你无法独自解决，升级而不是掩盖。

### 8. Read Before You Write（先读，再写）

**添加代码前，先读：导出接口、直接调用方、共享工具函数。**

「我觉得和这段没关系」是危险信号。
如果不确定代码为什么这么组织，先问。
理解现有调用图，防止孤儿函数、破坏契约、微妙回归。

### 9. Tests Verify Intent, Not Just Behavior
（测试验证意图，不只是行为）

**测试必须编码「为什么这个行为重要」，而不仅仅是「它做了什么」。**

业务逻辑变更时不会失败的测试是无效的。
用意图命名测试。断言那些映射到用户价值的输出。
行为改变时测试应该失败——而且失败应该告诉你哪个用户承诺被打破了。

### 10. Checkpoint at Every Meaningful Step
（每完成一个重要步骤就打检查点）

**总结：做了什么、验证了什么、还剩什么。**

不要从一个你无法描述清楚的状态继续下一步。
搞丢上下文了就停下来，重新说明。

好的检查点：
```
Done: [已完成的工作]
Verified: [测试/证据]
Remaining: [下一步]
```

### 11. Follow Convention, Even If You Disagree
（遵循代码库惯例，即使你不同意）

**在代码库内部，遵循 > 个人偏好。**

如果你真的认为某个惯例有害，明确指出来，不要偷偷引入新方案。
一致性是一个功能。分歧的成本比你的意见更持久。

### 12. Fail Loudly（失败要出声）

**「完成了」是错的——如果有东西被静默跳过了。**
**「测试通过」是错的——如果有测试被跳过了。**

默认暴露不确定性，不要藏着掖着。
静默失败会累积。大声失败会被修复。

遇到模糊性、边界情况或不完整的验证时，说出来——不要平滑过去。

---

## 适用性说明

**适用于 Codex 吗？是的。**

12 条原则是纯行为准则，不依赖任何特定工具：
- 没有脚本依赖
- 没有 MCP server 要求
- 没有钩子函数
- 纯粹的 AI 行为指导

| 原则 | Claude Code | Codex | Cursor |
|------|:--:|:--:|:--:|
| Think Before Coding | ✅ | ✅ | ✅ |
| Simplicity First | ✅ | ✅ | ✅ |
| Surgical Changes | ✅ | ✅ | ✅ |
| Goal-Driven Execution | ✅ | ✅ | ✅ |
| Model for Judgment, Code for Determinism | ✅ | ✅ | ✅ |
| Token Budget Is Law | ✅ | ✅ | ✅ |
| Surface Conflicts, Don't Compromise | ✅ | ✅ | ✅ |
| Read Before You Write | ✅ | ✅ | ✅ |
| Tests Verify Intent, Not Just Behavior | ✅ | ✅ | ✅ |
| Checkpoint at Every Meaningful Step | ✅ | ✅ | ✅ |
| Follow Convention, Even If You Disagree | ✅ | ✅ | ✅ |
| Fail Loudly | ✅ | ✅ | ✅ |

---

## Claude Code 安装

### 方法 A：通过插件市场安装（推荐）

```bash
# 1. 添加市场源
claude plugins marketplace add forrest https://github.com/forrestchang/andrej-karpathy-skills

# 2. 安装插件
claude plugins install andrej-karpathy-skills@forrest

# 3. 验证
claude plugins list
```

### 方法 B：手动安装技能到全局

```bash
# 1. 克隆仓库
git clone https://github.com/forrestchang/andrej-karpathy-skills.git /tmp/ak-skills

# 2. 创建共享技能目录并复制
mkdir -p ~/.agents/skills
cp -r /tmp/ak-skills/skills/karpathy-guidelines ~/.agents/skills/karpathy-guidelines

# 3. 创建软链接到 Claude Code skills 目录
mkdir -p ~/.claude/skills
ln -sfn ~/.agents/skills/karpathy-guidelines ~/.claude/skills/karpathy-guidelines

# 4. 清理
rm -rf /tmp/ak-skills
```

### 方法 C：项目级使用

直接在项目根目录放置 `CLAUDE.md`：

```bash
cp /path/to/CLAUDE.md ~/your-project/CLAUDE.md
```

### 方法 D：自定义扩展版（本仓库方式）

如需使用扩展版 12 条原则：

```bash
# 从本仓库复制扩展版 SKILL.md
cp skills/karpathy-guidelines/SKILL.md ~/.agents/skills/karpathy-guidelines/SKILL.md

# Claude Code 是软链接，自动生效
# Codex 需要复制
rm -rf ~/.codex/skills/karpathy-guidelines
cp -r ~/.agents/skills/karpathy-guidelines ~/.codex/skills/karpathy-guidelines
```

---

## OpenAI Codex 安装

### 方法 A：通过 skill-installer 安装（推荐）

```bash
# 使用 Codex 内置安装脚本
python3 ~/.codex/skills/.system/skill-installer/scripts/install-skill-from-github.py \
  --repo forrestchang/andrej-karpathy-skills \
  --path skills/karpathy-guidelines \
  --name karpathy-guidelines
```

### 方法 B：手动安装到全局

```bash
# 1. 克隆仓库
git clone https://github.com/forrestchang/andrej-karpathy-skills.git /tmp/ak-skills

# 2. 复制技能到 Codex 全局 skills 目录
cp -r /tmp/ak-skills/skills/karpathy-guidelines ~/.codex/skills/karpathy-guidelines

# 3. 清理
rm -rf /tmp/ak-skills

# 4. 重启 Codex 生效
```

> 验证：`ls -la ~/.codex/skills/karpathy-guidelines/SKILL.md`

### 方法 C：项目级使用

在项目根目录创建或修改 `AGENTS.md`，加入：

```markdown
@~/.codex/skills/karpathy-guidelines/SKILL.md
```

或直接将 12 条原则内容合并到项目的 `AGENTS.md` 中。

### 方法 D：自定义扩展版（本仓库方式）

如需使用扩展版 12 条原则：

```bash
# 从本仓库复制扩展版 SKILL.md
cp skills/karpathy-guidelines/SKILL.md ~/.codex/skills/karpathy-guidelines/SKILL.md
```

---

## Cursor 安装

### 方法 A：项目级规则（推荐）

```bash
# 1. 克隆仓库
git clone https://github.com/forrestchang/andrej-karpathy-skills.git /tmp/ak-skills

# 2. 复制规则到项目
cp /tmp/ak-skills/.cursor/rules/karpathy-guidelines.mdc ~/your-project/.cursor/rules/

# 3. 清理
rm -rf /tmp/ak-skills
```

规则文件中的 `alwaysApply: true` 会在每个对话中自动生效。

### 方法 B：全局 .cursorrules

```bash
# 将 12 条原则核心内容追加到全局 rules 文件
cat skills/karpathy-guidelines/SKILL.md >> ~/.cursorrules
```

### 方法 C：个人技能目录

```bash
mkdir -p ~/.cursor/skills
cp -r /tmp/ak-skills/skills/karpathy-guidelines ~/.cursor/skills/karpathy-guidelines
```

---

## 验证安装

### Claude Code 验证

```bash
# 查看已安装技能
ls -la ~/.claude/skills/karpathy-guidelines

# 或通过插件列表
claude plugins list | grep karpathy

# 在对话中测试
# 说："列出你的可用技能"
# 应该能看到 karpathy-guidelines
```

### Codex 验证

```bash
# 查看已安装技能
ls -la ~/.codex/skills/karpathy-guidelines

# 重启 Codex 后
codex
# 说："你有什么技能？"
# 应该列出 karpathy-guidelines
```

### Cursor 验证

```bash
# 检查规则文件
cat ~/your-project/.cursor/rules/karpathy-guidelines.mdc | head -5

# 或在 Cursor 中
# Settings → Rules → 应该能看到 karpathy-guidelines
```

---

## 使用注意事项

### 与其他规则的关系

**Karpathy Guidelines 会增强但不会取代你现有的 AGENTS.md / CLAUDE.md 规则。**

| 现有规则 | Karpathy Guidelines 叠加效果 |
|----------|------------------------------|
| RTK (`~/.codex/RTK.md`) | 独立运作，不冲突 |
| Caveman | 独立运作，不冲突 |
| AGENTS.md (Superpowers) | Karpathy 的「先思考」和 Superpowers 的「无设计不实现」互补 |
| 项目 AGENTS.md | Karpathy 提供全局质量基线 |

> **AGENTS.md 的优先级规则**：更具体的 AGENTS.md（项目级）覆盖更通用的。Karpathy Guidelines 作为全局技能，在项目没有冲突规则时生效。

### 可能需要调整的地方

12 条原则偏保守（「谨慎优于速度」），在某些场景下可以灵活处理：

- **简单任务**：比如改一个字符串、加一行日志，不必逐条走全部原则
- **新建项目**：新建时没有「现有风格」需要匹配，Surgical Changes 原则自然放宽
- **快速原型**：如果用户明确说「快速出一个原型」，可以降低简洁标准的严格执行
- **Token 预算**：本地开发如果上下文足够，4K/30K 的硬限制可以放宽，但**暴露超预算的意识不能丢**

---

## 与其他技能的配合

```bash
# 建议同时安装以下技能：

# 1. RTK - 命令输出压缩
brew install rtk

# 2. Caveman - AI 表达压缩
mkdir -p ~/.agents/skills/caveman
# 复制 SKILL.md 到该目录

# 3. Karpathy Guidelines - 编码质量基线（12 条原则）
# 按本文档安装

# 4. Superpowers - 完整工作流
claude plugins install superpowers@superpowers-dev

# 5. gstack - 虚拟工程团队（CEO/设计/QA/安全/发布）
# 见 install-gstack.md

# 6. ui-ux-pro-max - UI/UX 设计智能
# 见 install-ui-ux-pro-max.md
```

**推荐顺序**：先装 RTK + Caveman（省 token），再装 Karpathy Guidelines + Superpowers（提质量），最后用 gstack + ui-ux-pro-max（扩能力）。

---

## 附录：配置文件路径速查

```
# Karpathy Guidelines（扩展版 12 条）
~/.codex/skills/karpathy-guidelines/   # Codex 全局技能
~/.claude/skills/karpathy-guidelines   # → 软链接到共享目录（Claude Code）
~/.agents/skills/karpathy-guidelines/  # 共享技能源文件
cursor/rules/karpathy-guidelines.mdc   # Cursor 项目规则

# 本仓库扩展版
skills/karpathy-guidelines/SKILL.md     # 12 条原则完整定义

# 源仓库结构
CLAUDE.md                               # Claude Code 指令文件
skills/karpathy-guidelines/SKILL.md     # 原版 4 条原则
.cursor/rules/karpathy-guidelines.mdc   # Cursor 规则
EXAMPLES.md                             # 使用示例
```

---

## 参考链接

- [源仓库](https://github.com/forrestchang/andrej-karpathy-skills)
- [Karpathy 原始推文](https://x.com/karpathy/status/2015883857489522876)
- [README.zh.md (中文)](https://github.com/forrestchang/andrej-karpathy-skills/blob/main/README.zh.md)
