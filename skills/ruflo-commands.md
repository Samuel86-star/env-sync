# Ruflo 命令参考手册

> Ruflo v3.7.0-alpha.11 — AI Agent 编排平台（60+ agents、swarm intelligence、MCP 集成）
>
> 安装：`npm install -g ruflo`

---

## 一、基础命令

| 命令 | 说明 |
|------|------|
| `ruflo init` | 初始化 Ruflo 到当前目录 |
| `ruflo start` | 启动编排系统 |
| `ruflo status` | 查看系统状态 |
| `ruflo --version` | 查看版本 |
| `ruflo doctor` | 系统诊断和健康检查 |
| `ruflo config` | 配置管理 |

---

## 二、Agent 管理

```bash
ruflo agent spawn -t coder          # 创建 agent
ruflo agent list                    # 列出所有 agent
ruflo agent status agent-001        # 查看 agent 详情
ruflo agent stop agent-001          # 停止 agent
ruflo agent metrics                 # agent 性能指标
ruflo agent health                  # agent 健康检查
ruflo agent logs                    # agent 活动日志
ruflo agent pool                    # agent 池管理（扩缩容）
```

**WASM 沙箱 Agent：**
```bash
ruflo agent wasm-status             # 检查 WASM 可用性
ruflo agent wasm-create             # 创建 WASM 沙箱 agent
ruflo agent wasm-prompt             # 向 WASM agent 发送 prompt
ruflo agent wasm-gallery            # 浏览 WASM agent 模板
```

---

## 三、Swarm 协调

```bash
ruflo swarm init --v3-mode          # 初始化 V3 swarm（15-agent 层次化 Mesh）
ruflo swarm start -o "Build API"    # 启动 swarm
ruflo swarm status                  # 查看 swarm 状态
ruflo swarm stop                    # 停止 swarm
ruflo swarm scale                   # 扩缩 agent 数量
ruflo swarm coordinate --agents 15  # V3 协调执行
```

---

## 四、Hive Mind（女王共识协调）

```bash
ruflo hive-mind init -t hierarchical-mesh   # 初始化 hive
ruflo hive-mind spawn -n 5                  # 生成 5 个 worker
ruflo hive-mind spawn --claude -o "任务"    # 启动 Claude Code + hive
ruflo hive-mind task -d "实现功能"           # 提交任务
ruflo hive-mind status                      # 查看状态
ruflo hive-mind broadcast "消息"            # 广播到所有 worker
ruflo hive-mind memory                      # 访问共享内存
ruflo hive-mind consensus                   # 共识提案和投票
ruflo hive-mind shutdown                    # 关闭 hive
```

---

## 五、任务管理

```bash
ruflo task create -t implementation -d "Add user auth"   # 创建任务
ruflo task list                                          # 列出任务
ruflo task list --all                                    # 列出所有任务
ruflo task status task-123                               # 查看任务详情
ruflo task assign task-123 --agent coder-1               # 分配任务给 agent
ruflo task cancel task-123                               # 取消任务
ruflo task retry task-123                                # 重试失败任务
```

---

## 六、智能路由（Q-Learning）

```bash
ruflo route "implement feature"        # Q-Learning 自动路由到最优 agent
ruflo route "write tests" --q-learning # 强制使用 Q-Learning
ruflo route --agent coder "fix bug"    # 指定 agent 路由
ruflo route list-agents                # 列出可路由 agent 类型
ruflo route stats                      # 路由统计
ruflo route feedback                   # 反馈路由决策（强化学习）
ruflo route coverage                   # 基于测试覆盖的路由
```

---

## 七、内存与向量搜索

```bash
ruflo memory init                            # 初始化内存（sql.js WASM SQLite + 向量嵌入）
ruflo memory store -k "key" -v "value"       # 存储
ruflo memory search -q "auth patterns"       # 语义搜索
ruflo memory retrieve                        # 获取数据
ruflo memory list                            # 列出条目
ruflo memory stats                           # 统计信息
ruflo memory cleanup                         # 清理过期数据
ruflo memory compress                        # 压缩优化
ruflo memory export                          # 导出
ruflo memory import                          # 导入
```

---

## 八、MCP 服务器管理

```bash
ruflo mcp start                        # 启动 MCP 服务器
ruflo mcp start -t http -p 8080        # HTTP 模式 + 端口
ruflo mcp status                       # 查看状态
ruflo mcp tools                        # 列出可用 MCP 工具
ruflo mcp exec                         # 执行 MCP 工具
ruflo mcp toggle                       # 启用/禁用工具
ruflo mcp logs                         # 查看日志
ruflo mcp restart                      # 重启
ruflo mcp stop                         # 停止
```

---

## 九、AutoPilot（持久化自动完成）

```bash
ruflo autopilot enable                 # 启用（agent 持续工作直到所有任务完成）
ruflo autopilot status                 # 查看进度
ruflo autopilot config --max-iterations 100 --timeout 180  # 配置限制
ruflo autopilot disable                # 禁用
ruflo autopilot reset                  # 重置计数器
ruflo autopilot predict                # 预测下一步最优操作
ruflo autopilot learn                  # 从历史中学习成功模式
ruflo autopilot log                    # 查看事件日志
```

---

## 十、安全与代码分析

```bash
# 安全扫描
ruflo security                         # CVE 检测、威胁建模、AI 防御

# 代码分析
ruflo analyze ast src/                 # AST 解析
ruflo analyze complexity src/ --threshold 15  # 复杂度检测
ruflo analyze symbols src/ --type function      # 符号提取
ruflo analyze imports src/ --external           # 依赖分析
ruflo analyze diff --risk                       # diff 风险评估
```

---

## 十一、会话与工作流

```bash
# 会话
ruflo session save -n "checkpoint-1"   # 保存会话
ruflo session restore session-123      # 恢复会话
ruflo session list                     # 列出会话
ruflo session current                  # 当前会话
ruflo session export -o backup.json    # 导出
ruflo session delete session-123       # 删除

# 工作流
ruflo workflow run -t development --task "Build feature"   # 执行工作流
ruflo workflow validate -f ./workflow.yaml                  # 校验定义
ruflo workflow list                                         # 列出现有工作流
ruflo workflow stop                                         # 停止工作流
ruflo workflow template                                     # 管理模板
```

---

## 十二、Guidance 控制面版

```bash
ruflo guidance compile                 # 编译 CLAUDE.md 为策略包
ruflo guidance retrieve -t "Fix auth bug"  # 检索任务相关 guidance
ruflo guidance gates -c "rm -rf /"     # 检查执行门控
ruflo guidance status                  # 查看控制面版状态
ruflo guidance optimize                # 分析和优化 CLAUDE.md
ruflo guidance ab-test                 # A/B 行为对比
```

---

## 十三、Hooks（自学习钩子）

```bash
ruflo hooks pre-edit                   # 编辑前上下文和建议
ruflo hooks post-edit                  # 记录编辑结果（强化学习）
ruflo hooks pre-command                # 命令执行前风险评估
```

---

## 十四、部署与 Provider 管理

```bash
ruflo deployment                       # 部署管理、环境、回滚
ruflo providers                        # AI provider/模型管理
ruflo plugins                          # 插件管理（IPFS 去中心化注册表）
ruflo process                          # 后台进程管理
ruflo daemon                           # 后台 worker 守护进程
ruflo cleanup                          # 清理项目产物
ruflo claims                           # 声明式授权和权限
ruflo issues                           # 协作式 issue（人+agent 工作流）
ruflo appliance                        # RVFA 设备管理
ruflo completions                      # 生成 shell 自动补全
ruflo update                           # 更新 @claude-flow 包
ruflo migrate                          # V2 → V3 迁移
```

---

## 十五、全局选项

| 选项 | 说明 |
|------|------|
| `-h, --help` | 帮助信息 |
| `-V, --version` | 版本号 |
| `-v, --verbose` | 详细输出 |
| `-Q, --quiet` | 静默模式 |
| `-c, --config` | 指定配置文件路径 |
| `--format` | 输出格式（text/json/table） |
| `--no-color` | 禁用颜色 |
| `-i, --interactive` | 交互模式 |

---

## 典型工作流

### 项目启动
```bash
ruflo init && ruflo start
```

### 任务分派 + 自动完成
```bash
ruflo task create -t implementation -d "实现用户认证"
ruflo route "实现用户认证"        # Q-Learning 自动选择 agent
ruflo autopilot enable           # agent 持续工作直到完成
```

### 代码审查
```bash
ruflo analyze diff --risk
ruflo guidance gates -c "PR diff"
```

### 团队协作
```bash
ruflo hive-mind init -t hierarchical-mesh
ruflo hive-mind spawn --claude -o "开发新功能"
```
