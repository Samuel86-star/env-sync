# StarRocks 安装与配置指南（macOS 轻量版）

> 适用场景：本地功能测试、SQL 语法验证、轻量 OLAP 体验
> 部署方式：Docker All-in-One（单容器，一键启动）
> 预计时间：5 分钟
> 更新时间：2026-05-11

---

## 方案选择

| 方案 | 适用场景 | 资源占用 | 复杂度 | 推荐度 |
|------|----------|:--------:|:------:|:------:|
| **Docker All-in-One** | 本地测试、快速体验 | 4-8GB 内存 | ⭐ 极简 | ✅ **首选** |
| Docker Compose 多节点 | 模拟集群、压测 | 8GB+ 内存 | ⭐⭐ 中等 | 可选 |
| 二进制手动部署 | 生产环境、深度调优 | 16GB+ 内存 | ⭐⭐⭐ 复杂 | ❌ 不推荐本地 |

**本指南采用 Docker All-in-One 方案**，一条命令启动，一条命令停止，数据可持久化。

---

## 前置条件

### 1. Docker Desktop

```bash
# 验证 Docker 是否已安装
docker --version

# 验证 Docker 是否运行中
docker ps
```

如未安装，从 [Docker 官网](https://www.docker.com/products/docker-desktop) 下载安装。

### 2. 内存要求

| 场景 | 建议内存 |
|------|:--------:|
| 纯功能测试（小数据量） | 4GB 给 Docker |
| 中等数据量测试 | 6-8GB 给 Docker |

**Docker Desktop 设置：**
`Settings → Resources → Memory` 拉到至少 4GB。

### 3. Apple Silicon (M1/M2/M3) 注意事项

StarRocks 官方镜像是 `linux/amd64` 架构，Apple Silicon 需要开启 Rosetta 转译：

```bash
# Docker Desktop 设置中开启：
# Settings → General → Use Rosetta for x86/amd64 emulation on Apple Silicon
```

> 如果未开启，启动时会报 `exec format error`。

---

## 安装步骤

### 步骤 1：拉取镜像

```bash
docker pull starrocks/allin1-ubuntu:3.3-latest
```

> `3.3-latest` 是 3.3 稳定版最新 tag。如需固定版本，替换为 `3.3.0` 等。

### 步骤 2：启动容器（带数据持久化）

```bash
# 创建本地数据目录
mkdir -p ~/starrocks-data

# 启动 All-in-One 容器
docker run -d \
  --name starrocks \
  --privileged \
  -p 9030:9030 \
  -p 8030:8030 \
  -p 8040:8040 \
  -v ~/starrocks-data:/data/deploy/starrocks \
  starrocks/allin1-ubuntu:3.3-latest
```

**端口说明：**

| 端口 | 用途 | 协议 |
|:----:|------|------|
| `9030` | SQL 查询（MySQL 协议） | MySQL Client / JDBC |
| `8030` | FE HTTP 管理界面 | HTTP |
| `8040` | BE HTTP 监控 | HTTP |

**参数说明：**

| 参数 | 说明 |
|------|------|
| `-d` | 后台运行 |
| `--name starrocks` | 容器名，方便管理 |
| `--privileged` | StarRocks BE 需要部分系统权限 |
| `-v ~/starrocks-data:/data/deploy/starrocks` | 数据持久化到本地 |

### 步骤 3：等待启动完成

```bash
# 查看日志，等待 "StarRocks is ready" 或类似提示
docker logs -f starrocks
```

首次启动约需 **30-60 秒**。看到以下输出即可：

```
StarRocks FE and BE are ready.
```

按 `Ctrl+C` 退出日志追踪（容器继续后台运行）。

---

## 连接与验证

### 方式 1：MySQL 客户端连接

StarRocks 兼容 MySQL 协议，可用任意 MySQL 客户端：

```bash
# 用系统自带的 mysql 客户端
mysql -h 127.0.0.1 -P 9030 -u root --prompt="StarRocks> "

# 或者用 Docker 内置的 mysql
mysql -h 127.0.0.1 -P 9030 -u root -e "SHOW DATABASES;"
```

**默认账户：**
- 用户名：`root`
- 密码：空（无需密码）
- 端口：`9030`

### 方式 2：HTTP 管理界面

浏览器打开 http://127.0.0.1:8030

可查看 FE 状态、查询历史、系统监控。

### 方式 3：快速验证 SQL

```sql
-- 查看版本
SELECT current_version();

-- 查看所有数据库
SHOW DATABASES;

-- 创建测试数据库
CREATE DATABASE IF NOT EXISTS test_db;

-- 创建测试表（Duplicate Key 模型）
USE test_db;

CREATE TABLE user_events (
    event_id BIGINT,
    user_id BIGINT,
    event_time DATETIME,
    event_type VARCHAR(20),
    amount DECIMAL(10,2)
)
DUPLICATE KEY(event_id, user_id)
DISTRIBUTED BY HASH(user_id) BUCKETS 10
PROPERTIES ("replication_num" = "1");

-- 插入测试数据
INSERT INTO user_events VALUES
(1, 1001, '2026-05-11 10:00:00', 'click', 0.00),
(2, 1002, '2026-05-11 10:05:00', 'purchase', 99.50),
(3, 1001, '2026-05-11 10:10:00', 'click', 0.00);

-- 查询
SELECT event_type, COUNT(*) as cnt, SUM(amount) as total
FROM user_events
GROUP BY event_type;
```

---

## 常用操作

### 启停容器

```bash
# 停止
docker stop starrocks

# 启动
docker start starrocks

# 重启
docker restart starrocks
```

### 查看状态

```bash
# 容器状态
docker ps | grep starrocks

# 查看日志（最后 50 行）
docker logs --tail 50 starrocks

# 进入容器内部
docker exec -it starrocks /bin/bash
```

### 数据备份与迁移

```bash
# 备份数据目录（容器停止后）
cp -r ~/starrocks-data ~/starrocks-data-backup-$(date +%Y%m%d)

# 迁移到新机器：复制 ~/starrocks-data 目录，重新 docker run 挂载即可
```

### 完全卸载

```bash
# 停止并删除容器
docker stop starrocks
docker rm starrocks

# 删除镜像（可选）
docker rmi starrocks/allin1-ubuntu:3.3-latest

# 删除数据（⚠️ 不可逆）
rm -rf ~/starrocks-data
```

---

## 故障排查

### Q1: 启动报错 `exec format error`

**原因：** Apple Silicon 未开启 Rosetta 转译。

**解决：**
```bash
# Docker Desktop → Settings → General
# 勾选 "Use Rosetta for x86/amd64 emulation on Apple Silicon"
# 重启 Docker Desktop
```

### Q2: 连接 9030 端口超时

**原因：** 容器还在启动中，或内存不足导致启动失败。

**解决：**
```bash
# 查看日志确认状态
docker logs --tail 100 starrocks

# 检查内存分配
docker stats starrocks

# 如果内存不足，增加 Docker Desktop 内存限制后重启容器
```

### Q3: `mysql` 命令找不到

```bash
# macOS 未安装 MySQL 客户端，可用 Homebrew 安装
brew install mysql-client

# 或者直接用 Docker 运行 mysql 客户端
docker run -it --rm mysql:8.0 mysql -h host.docker.internal -P 9030 -u root
```

### Q4: 数据目录权限错误

```bash
# 确保本地目录有写权限
chmod 755 ~/starrocks-data

# 或重新创建
docker stop starrocks
docker rm starrocks
rm -rf ~/starrocks-data
mkdir -p ~/starrocks-data
docker run -d ...（重新执行启动命令）
```

### Q5: BE 启动失败或崩溃

**原因：** `--privileged` 未加，或 macOS 安全限制。

**解决：** 确认启动命令包含 `--privileged` 参数。

---

## 与其他数据库的对比

| 特性 | StarRocks | MySQL | ClickHouse |
|------|-----------|:-----:|:----------:|
| 定位 | OLAP（分析型） | OLTP（事务型） | OLAP（分析型） |
| 本地测试 | Docker All-in-One | `brew install mysql` | Docker |
| 启动速度 | 30-60 秒 | 5 秒 | 10-20 秒 |
| 内存占用 | 4GB+ | 256MB+ | 2GB+ |
| 适合场景 | 大数据分析、实时报表 | 业务系统、CRUD | 日志分析、时序数据 |

---

## 参考链接

- [StarRocks 官方文档](https://docs.starrocks.io/)
- [StarRocks Docker 快速开始](https://docs.starrocks.io/docs/quick_start/deploy_with_docker/)
- [StarRocks GitHub](https://github.com/StarRocks/starrocks)
