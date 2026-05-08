# Claude Code Slash Commands 参考手册

> 整理自 Claude Code 官方文档及实际高效用法。
> gstack 角色命令见 `skills/gstack-commands.md`。
> 
> 启动 Claude Code 后输入 `/` 即可查看所有可用命令。

---

## 一、会话管理

| 命令 | 说明 |
|------|------|
| `/clear` | 清空当前会话上下文 |
| `/compact` | 压缩会话上下文（保留关键信息，释放 token） |
| `/context` | 查看当前上下文使用情况 |
| `/cost` | 查看当前会话 API 费用 |
| `/help` | 查看帮助信息 |
| `/logout` | 退出登录 |

## 二、文件操作

| 命令 | 说明 |
|------|------|
| `/add-dir` | 添加目录到上下文 |
| `/ide` | 在 IDE 中打开当前文件 |
| `/init` | 初始化 CLAUDE.md 项目配置 |
| `/terminal-setup` | 配置终端集成 |
| `/doctor` | 诊断 Claude Code 安装问题 |

## 三、工作流

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

---

## 四、高效工作流速查

### 单行起飞

```bash
# 快速启动 Claude + 加载项目上下文
claude --model haiku --system-prompt-file CLAUDE.md

# 压缩长会话
/compact
```

### 代码审查流程

```bash
# 方式一：通过 Claude 直接审查
claude -p "review this PR" < diff.patch

# 方式二：交互式
/review
```

---

## 五、自定义斜杠命令

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

## 六、键盘快捷键

| 快捷键 | 说明 |
|--------|------|
| `Ctrl+C` | 中断当前操作 |
| `Ctrl+D` | 退出 Claude Code |
| `Ctrl+L` | 清屏 |
| `Ctrl+R` | 搜索历史命令 |
| `Esc` | 退出当前模式 |
| `↑/↓` | 浏览命令历史 |

---

## 七、Tips

1. **先规划，再执行** — 复杂任务先 `/init` 建立 CLAUDE.md 项目上下文
2. **会话过长时 `/compact`** — 释放 token 保持响应质量
3. **发布前自检** — `/review` → `/security-review` → 合并
4. **善用 `claude -p` 管道模式** — 适合脚本化和 CI/CD 集成
5. **gstack 角色命令** — 见 `skills/gstack-commands.md`（规划/设计/质量/安全/发布/浏览器/元工具）
