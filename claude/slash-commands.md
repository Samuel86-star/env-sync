# Claude Code Slash Commands 参考手册

> 整理自 Claude Code 官方文档、gstack 虚拟工程团队、实际高效用法。
> 
> 启动 Claude Code 后输入 `/` 即可查看所有可用命令。

---

## 一、基础命令（Claude Code 内置）

### 会话管理

| 命令 | 说明 |
|------|------|
| `/clear` | 清空当前会话上下文 |
| `/compact` | 压缩会话上下文（保留关键信息，释放 token） |
| `/context` | 查看当前上下文使用情况 |
| `/cost` | 查看当前会话 API 费用 |
| `/help` | 查看帮助信息 |
| `/logout` | 退出登录 |

### 文件操作

| 命令 | 说明 |
|------|------|
| `/add-dir` | 添加目录到上下文 |
| `/ide` | 在 IDE 中打开当前文件 |
| `/init` | 初始化 CLAUDE.md 项目配置 |
| `/terminal-setup` | 配置终端集成 |
| `/doctor` | 诊断 Claude Code 安装问题 |

### 工作流

| 命令 | 说明 |
|------|------|
| `/review` | 代码审查（review PR / diff） |
| `/pr-comments` | 查看 PR 评论 |
| `/release-notes` | 生成发布说明 |
| `/security-review` | 安全审查 |
| `/bug-report` | 生成 Bug 报告 |
| `/todos` | 管理待办事项 |
| `/mcp` | 管理 MCP 服务器 |
| `/agents` | 管理子 agent |
| `/bashes` | 管理后台 shell 任务 |
| `/todos` | 管理待办事项 |

---

## 二、gstack 角色命令（23 个团队角色）

> 需先安装 gstack：`brew install bun && git clone https://github.com/garrytan/gstack.git ~/.claude/skills/gstack`

### 规划评审层

| 命令 | 角色 | 说明 |
|------|------|------|
| `/plan-ceo-review` | CEO 审视 | 重新思考问题，挑战前提，扩展产品视野 |
| `/plan-eng-review` | 工程评审 | 架构、数据流、边界情况、测试覆盖 |
| `/plan-design-review` | 设计评审 | 审美/排版/色彩/动效 0-10 分 + 修复建议 |
| `/plan-devex-review` | 开发者体验 | 文档、上手时间、魔法时刻设计 |
| `/autoplan` | 自动评审 | 串联 CEO + 设计 + 工程 + DX 四次评审 |

### 实现层

| 命令 | 角色 | 说明 |
|------|------|------|
| `/design-shotgun` | 设计发散 | 生成多套 AI 设计方案变体，收集反馈迭代 |
| `/design-consultation` | 设计咨询 | 完整设计系统：美学、排版、颜色、布局 |
| `/design-html` | 设计落地 | 将设计稿转成生产级 HTML/CSS |
| `/design-review` | 设计走查 | 发现视觉不一致、间距问题、AI 痕迹 |

### 质量层

| 命令 | 角色 | 说明 |
|------|------|------|
| `/qa` | QA 测试 | 系统测试 + 自动修 Bug |
| `/qa-only` | 只测不修 | 生成结构化 QA 报告 + 截图 + 复现步骤 |
| `/review` | PR 审查 | SQL 安全、LLM 信任边界、条件副作用 |
| `/health` | 代码健康 | 类型检查/格式化/测试/死代码 → 0-10 分 |

### 安全层

| 命令 | 角色 | 说明 |
|------|------|------|
| `/cso` | 首席安全官 | 秘钥考古、供应链、CI/CD 安全、AI 安全 |
| `/careful` | 安全护栏 | `rm -rf`/`DROP TABLE`/`force push` 拦截警告 |
| `/freeze` | 编辑锁定 | 限制编辑到指定目录，防止误改 |
| `/guard` | 全面保护 | `/careful` + `/freeze` 组合 |
| `/unfreeze` | 解锁编辑 | 解除 `/freeze` 限制 |

### 发布层

| 命令 | 角色 | 说明 |
|------|------|------|
| `/ship` | 发布 | 检测合并、跑测试、审查、bump 版本、CHANGELOG、PR |
| `/land-and-deploy` | 部署 | 合并 PR、等待 CI、验证生产 |
| `/document-release` | 文档更新 | 同步 README/ARCHITECTURE/CHANGELOG |
| `/canary` | 金丝雀监控 | 监控生产错误、性能回归、截图对比 |

### 浏览器与调试

| 命令 | 角色 | 说明 |
|------|------|------|
| `/browse` | 无头浏览器 | 导航/交互/截图/断言，~100ms 级响应 |
| `/benchmark` | 性能基准 | Core Web Vitals、页面加载、资源大小基线 |
| `/scrape` | 网页抓取 | 原型→JSON，后续自动路由到固化脚本 |
| `/skillify` | 固化脚本 | 将成功的 `/scrape` 流程固化为技能 |

### 元工具

| 命令 | 角色 | 说明 |
|------|------|------|
| `/retro` | 周回顾 | 提交历史、工作模式、代码质量趋势 |
| `/office-hours` | YC Office Hours | 创业六问：需求验证、现状、细分市场 |
| `/learn` | 知识管理 | 查看/搜索/清理跨会话学到的内容 |
| `/context-save` | 上下文保存 | 保存 git 状态 + 决策 + 遗留工作 |
| `/context-restore` | 上下文恢复 | 恢复之前保存的会话状态 |
| `/make-pdf` | PDF 生成 | Markdown → 出版物级 PDF |

---

## 三、高效工作流速查

### 单行起飞

```bash
# 快速启动 Claude + 加载项目上下文
claude --model haiku --system-prompt-file CLAUDE.md

# 压缩长会话
/compact

# 一键发布
/ship
```

### 代码审查流程

```bash
# 方式一：通过 Claude 直接审查
claude -p "review this PR" < diff.patch

# 方式二：交互式
/review

# 方式三：gstack 多角色审查
/cso          # 安全审查
/review       # PR 代码审查
/qa           # 功能测试 + 自动修 Bug
```

### 设计流程

```bash
/design-consultation   # 1. 确定设计系统
/design-shotgun        # 2. 发散设计方案
/design-review         # 3. 走查（0-10 分）
/design-html           # 4. 产出 HTML
```

### 发布流程

```bash
/ship                  # 1. 代码发布
/canary                # 2. 上线后监控
/document-release      # 3. 同步文档
```

---

## 四、自定义斜杠命令

可以在 `~/.claude/settings.json` 或项目 `CLAUDE.md` 中定义自定义斜杠命令：

```json
{
  "slashCommands": [
    {
      "command": "/deploy",
      "description": "一键部署到生产环境",
      "prompt": "运行 npm run build && npm run deploy，成功后运行 /canary 监控"
    }
  ]
}
```

---

## 五、键盘快捷键

| 快捷键 | 说明 |
|--------|------|
| `Ctrl+C` | 中断当前操作 |
| `Ctrl+D` | 退出 Claude Code |
| `Ctrl+L` | 清屏 |
| `Ctrl+R` | 搜索历史命令 |
| `Esc` | 退出当前模式 |
| `↑/↓` | 浏览命令历史 |

---

## 六、Tips

1. **先规划，再执行** — 用 `/plan-ceo-review` + `/plan-eng-review` 避免返工
2. **安全第一** — 涉及破坏性操作前执行 `/guard`
3. **会话过长时 `/compact`** — 释放 token 保持响应质量
4. **发布前自检** — `/review` → `/qa` → `/cso`
5. **善用 `claude -p` 管道模式** — 适合脚本化和 CI/CD 集成
