# Ruflo 安装与配置指南

> Ruflo — 企业级 AI Agent 编排平台（60+ 专用 agents、swarm intelligence、MCP 集成）
> 
> 版本: v3.7.0-alpha.11 | 仓库: [ruvnet/claude-flow](https://github.com/ruvnet/claude-flow)

## 核心能力

| 能力 | 说明 |
|------|------|
| **Swarm Coordination** | 15-agent 层次化 Mesh 协调 |
| **AgentDB + HNSW** | 150x-12,500x 向量搜索加速 |
| **Flash Attention** | 2.49x-7.47x 推理加速 |
| **Self-learning Hooks** | 智能工作流自动化 |
| **MCP Integration** | Model Context Protocol 原生支持 |

## 安装

```bash
# 全局安装
npm install -g ruflo

# 验证
ruflo --version   # 应输出: ruflo v3.7.0-alpha.11
```

## 多环境配置

### 1. Claude Code CLI

直接可用，`/opt/homebrew/bin` 已在终端 PATH 中。

### 2. Claude Code Desktop

Desktop 不读 `.zshrc`，需设置 launchd 环境变量：

```bash
# 一次性设置（重启后失效）
launchctl setenv PATH "/opt/homebrew/bin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin"

# 持久化方案 — 创建 LaunchAgent
mkdir -p ~/Library/LaunchAgents
cat > ~/Library/LaunchAgents/com.ruflo.env.plist << 'PLIST'
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.ruflo.env</string>
    <key>ProgramArguments</key>
    <array>
        <string>/bin/launchctl</string>
        <string>setenv</string>
        <string>PATH</string>
        <string>/opt/homebrew/bin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin</string>
    </array>
    <key>RunAtLoad</key>
    <true/>
</dict>
</plist>
PLIST

launchctl load ~/Library/LaunchAgents/com.ruflo.env.plist
```

### 3. Codex (Desktop/CLI)

直接可用，Codex 继承系统 PATH。

## 常用命令

```bash
# 初始化项目
ruflo init

# 启动编排系统
ruflo start

# 创建 agent
ruflo agent spawn -t coder

# 初始化 swarm
ruflo swarm init --v3-mode

# 语义搜索
ruflo memory search -q "关键词"

# 状态查看
ruflo status

# 系统诊断
ruflo doctor
```

## 注意事项

- 目前为 **alpha 版本**（3.7.0-alpha.11），API 可能变动
- 安装大小约 8.2 MB
- 依赖 `@claude-flow/cli` 作为底层引擎
- 建议在单个项目目录下 `ruflo init` 后使用

## 有用的命令

```bash
# 查看所有 agent 类型
ruflo agent --help

# 查看 swarm 模式
ruflo swarm --help

# 查看 MCP 管理
ruflo mcp --help
```
