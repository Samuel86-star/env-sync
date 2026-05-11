# UI-UX Pro Max 安装与使用指南

> 适用工具：Claude Code / OpenAI Codex
> 功能：UI/UX 设计智能 — 50+ 风格、161 色彩面板、57 字体配对、99 UX 准则、25 图表类型
> 更新时间：2026-05-09

---

## 安装

### 前置条件

源文件已存在于 `~/.agents/skills/ui-ux-pro-max/`，共享技能目录结构如下：

```
~/.agents/skills/ui-ux-pro-max/
├── SKILL.md      # 技能定义（44KB）
├── data/         # 色彩、字体、风格数据库
└── scripts/      # 辅助脚本
```

### Claude Code 安装（CLI + Desktop 共享）

Claude Code 支持软链接，直接链接到共享源：

```bash
ln -sfn ~/.agents/skills/ui-ux-pro-max ~/.claude/skills/ui-ux-pro-max
```

**验证：**
```bash
ls ~/.claude/skills/ui-ux-pro-max/SKILL.md
# 应显示 SKILL.md 文件
```

> Claude Code Desktop 与 CLI 共享 `~/.claude/skills/` 目录，只需配置一次。

---

### Codex 安装

Codex **不支持软链接**，需要复制整个目录：

```bash
cp -r ~/.agents/skills/ui-ux-pro-max ~/.codex/skills/ui-ux-pro-max
```

**验证：**
```bash
ls ~/.codex/skills/ui-ux-pro-max/SKILL.md
# 应显示 SKILL.md 文件
```

> **注意**：Codex 重启新会话后生效。已运行的会话不会重新加载技能。

---

### 快速安装（一键命令）

```bash
# Claude Code
ln -sfn ~/.agents/skills/ui-ux-pro-max ~/.claude/skills/ui-ux-pro-max

# Codex
cp -r ~/.agents/skills/ui-ux-pro-max ~/.codex/skills/ui-ux-pro-max

# 验证
ls ~/.claude/skills/ui-ux-pro-max/SKILL.md
ls ~/.codex/skills/ui-ux-pro-max/SKILL.md
```

---

## 核心能力速查

### 触发条件

| 场景 | 是否触发 |
|------|:--------:|
| 设计新页面（Landing Page / Dashboard / Admin / SaaS / Mobile App） | ✅ 必须 |
| 创建/重构 UI 组件（Button / Modal / Form / Table / Chart） | ✅ 必须 |
| 选择配色方案、字体系统、间距标准、布局系统 | ✅ 必须 |
| 代码评审 UI 体验、可访问性、视觉一致性 | ✅ 必须 |
| 实现导航结构、动画、响应式行为 | ✅ 必须 |
| 纯后端逻辑开发 | ❌ 跳过 |
| API 或数据库设计 | ❌ 跳过 |
| 基础设施/DevOps | ❌ 跳过 |

### 十大设计优先级

| 优先级 | 类别 | 影响 | 关键检查 |
|:------:|------|:----:|----------|
| 1 | **可访问性** | CRITICAL | 对比度 ≥4.5:1、Alt 文本、键盘导航、Aria-labels |
| 2 | **触摸与交互** | CRITICAL | 最小触摸 44×44px、8px+ 间距、加载反馈 |
| 3 | **性能** | HIGH | WebP/AVIF、懒加载、CLS < 0.1 |
| 4 | **风格选择** | HIGH | 匹配产品类型、一致性、SVG 图标（不用 emoji） |
| 5 | **布局与响应式** | HIGH | 移动优先断点、Viewport meta、无横向滚动 |
| 6 | **排版与色彩** | MEDIUM | 基准 16px、行高 1.5、语义化色彩 token |
| 7 | **动画** | MEDIUM | 时长 150-300ms、运动传达意义、空间连续性 |
| 8 | **表单与反馈** | MEDIUM | 可见标签、错误就近、渐进式披露 |
| 9 | **导航模式** | HIGH | 可预测的返回、底部导航 ≤5、深度链接 |
| 10 | **图表与数据** | LOW | 图例、工具提示、可访问色彩 |

### 50+ 设计风格

- `glassmorphism` — 玻璃拟态
- `claymorphism` — 黏土拟态
- `minimalism` — 极简主义
- `brutalism` — 粗野主义
- `neumorphism` — 新拟物
- `bento grid` — 便当网格
- `dark mode` — 深色模式
- `responsive` — 响应式
- `skeuomorphism` — 拟物化
- `flat design` — 扁平设计

### 10 个技术栈支持

React、Next.js、Vue、Svelte、SwiftUI、React Native、Flutter、Tailwind CSS、shadcn/ui、HTML/CSS

---

## 典型使用场景

### 场景 1：设计一个登录页面

```
用户：帮我设计一个 SaaS 产品的登录页面，用深色模式

AI 自动触发 ui-ux-pro-max，会：
- 推荐适合 SaaS 的风格（glassmorphism 或 minimalism）
- 提供 161 色彩面板中的深色主题配色
- 选择 57 字体配对中的专业组合
- 确保对比度 ≥4.5:1
- 输出 shadcn/ui + Tailwind 组件代码
```

### 场景 2：评审现有 UI 代码

```
用户：帮我看看这个 Dashboard 的代码，感觉哪里不对

AI 自动触发 ui-ux-pro-max，会检查：
- 色彩对比度是否达标
- 触摸目标是否 ≥44×44px
- 是否有 emoji 替代图标
- 动画时长是否在 150-300ms 范围
- 响应式断点是否合理
- 表单标签是否可见
```

### 场景 3：选择配色方案

```
用户：帮我选一个适合金融科技 App 的配色

AI 自动触发 ui-ux-pro-max，会：
- 从 161 色彩面板筛选金融科技常用色系
- 提供主色、辅助色、语义色（成功/警告/错误）
- 确保深色模式下的对比度达标
- 生成 Tailwind CSS 色彩 token
```

---

## 交付前自检清单

交付 UI 代码前，AI 会自动验证：

### 视觉质量
- [ ] 无 emoji 作为图标（使用 SVG）
- [ ] 所有图标来自同一家族风格
- [ ] 按压状态不引起布局抖动
- [ ] 使用语义化主题 token（无硬编码色值）

### 交互
- [ ] 可点击元素有明确的按压反馈
- [ ] 触摸目标 ≥44×44pt（iOS）或 ≥48×48dp（Android）
- [ ] 微交互时长 150-300ms，原生缓动
- [ ] 禁用状态视觉明确且不可交互
- [ ] 屏幕阅读器焦点顺序匹配视觉顺序

### 深色模式
- [ ] 主文本对比度 ≥4.5:1
- [ ] 次要文本对比度 ≥3:1
- [ ] 分割线/边框在两种模式下可见
- [ ] Modal/Drawer 遮罩透明度足够（通常 40-60% 黑）

### 布局
- [ ] 尊重安全区（刘海、底部手势条）
- [ ] 滚动内容不被固定栏遮挡
- [ ] 在小屏手机、大屏手机、平板（横竖屏）验证
- [ ] 保持 4/8dp 间距节奏
- [ ] 长文本在平板上有可读宽度（不撑满全屏）

### 可访问性
- [ ] 有意义的图片/图标有 accessibility label
- [ ] 表单字段有标签、提示和清晰错误信息
- [ ] 颜色不是唯一信息指示器
- [ ] 支持减弱动画和动态字体大小

---

## 与其他技能的配合

| 技能组合 | 使用场景 |
|----------|----------|
| ui-ux-pro-max + `/design-consultation` (gstack) | 完整设计系统搭建 |
| ui-ux-pro-max + `/design-review` (gstack) | UI 质量走查（0-10 分） |
| ui-ux-pro-max + `/design-shotgun` (gstack) | 多方案设计发散 |
| ui-ux-pro-max + Karpathy Simplicity First | 设计决策 + 代码简洁 |
| ui-ux-pro-max + shadcn/ui MCP | 组件搜索 + 示例代码 |

---

## 验证安装

### Claude Code 验证

```bash
# 查看已安装技能
ls ~/.claude/skills/ | grep ui-ux

# 在对话中测试
claude
# 说："帮我设计一个深色模式的 SaaS 登录页"
# AI 应该引用 ui-ux-pro-max 中的配色/字体/风格建议
```

### Codex 验证

```bash
# 查看已安装技能
ls ~/.codex/skills/ | grep ui-ux

# 重启 Codex 后测试
codex
# 说："帮我选一个适合博客的字体配对"
# AI 应该从 57 字体配对中给出推荐
```

---

## 文件路径速查

| 文件 | 路径 |
|------|------|
| 共享技能源 | `~/.agents/skills/ui-ux-pro-max/` |
| Claude Code 技能 | `~/.claude/skills/ui-ux-pro-max → ~/.agents/skills/ui-ux-pro-max` |
| Codex 技能 | `~/.codex/skills/ui-ux-pro-max/`（复制） |

---

## 参考

- ui-ux-pro-max 源：`~/.agents/skills/ui-ux-pro-max/SKILL.md`
- 色彩数据库：`~/.agents/skills/ui-ux-pro-max/data/`
