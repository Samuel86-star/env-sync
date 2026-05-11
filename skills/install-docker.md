# Docker Desktop 安装与配置指南（macOS）

> 适用场景：本地容器化开发、运行数据库（StarRocks / MySQL Docker 版）、构建/测试容器镜像
> 适用芯片：Apple Silicon (M1/M2/M3) 及 Intel Mac
> 预计时间：10 分钟
> 更新时间：2026-05-11

---

## 1. 前置检查

### 1.1 系统要求

| 项目 | 最低要求 | 推荐配置 |
|------|:--------:|:--------:|
| macOS 版本 | 11 Big Sur | 13 Ventura 或更新 |
| 内存 | 4 GB | 8 GB+ |
| 磁盘空间 | 10 GB 可用 | 50 GB+ |
| 芯片 | Intel / Apple Silicon | Apple Silicon (M1/M2/M3) |

### 1.2 确认当前状态

```bash
# 检查是否已安装
docker --version

# 检查是否已运行
docker info

# 检查芯片架构
uname -m
# Apple Silicon 输出: arm64
# Intel 输出: x86_64
```

如果上面命令有正常输出，说明 Docker 已安装，跳到 [第 4 节 验证](#4-验证)。

---

## 2. 下载与安装

### 2.1 下载 Docker Desktop

访问官方下载页面：

```
https://www.docker.com/products/docker-desktop
```

**选择正确的版本：**

| 芯片 | 下载选择 |
|------|----------|
| Apple Silicon (M1/M2/M3) | **Mac with Apple Chip** |
| Intel Mac | **Mac with Intel Chip** |

> 下错版本会导致启动报错或性能严重下降。不确定芯片类型？
> 点击屏幕左上角 🍎 → 关于本机 → 查看"芯片"或"处理器"。

### 2.2 安装步骤

1. 双击下载的 `.dmg` 文件
2. 将 **Docker** 图标拖拽到 **Applications** 文件夹
3. 从 **启动台** 或 **访达 → 应用程序** 打开 Docker Desktop
4. 首次启动会要求输入 Mac 登录密码（授权安装辅助工具）
5. 接受服务条款，完成初始化

### 2.3 Apple Silicon 额外配置（关键）

如果你用 M1/M2/M3 Mac，**必须开启 Rosetta 转译** 才能运行 x86 架构容器（如 StarRocks）：

```
Docker Desktop → Settings（齿轮图标）→ General
→ 勾选 "Use Rosetta for x86/amd64 emulation on Apple Silicon"
```

> 不开启此选项，运行 `starrocks/allin1-ubuntu` 等 x86 镜像时会报 `exec format error`。

---

## 3. 资源配置

Docker Desktop 默认分配的资源偏保守，建议根据你的 Mac 配置调整：

### 3.1 打开设置

```
Docker Desktop → Settings（齿轮图标）→ Resources
```

### 3.2 推荐配置（16GB M3 MacBook）

| 项目 | 默认值 | 推荐值 | 说明 |
|------|:------:|:------:|:----:|
| **CPUs** | 4 | 4-6 | 留 2 核给系统和 IDE |
| **Memory** | 2 GB | **6-8 GB** | StarRocks 需要 4GB+ |
| **Swap** | 1 GB | 2 GB | 防止内存不足时容器崩溃 |
| **Disk image size** | 64 GB | 64-128 GB | 根据项目数量调整 |
| **VirtioFS** | 开启 | ✅ 开启 | macOS 文件共享加速 |

### 3.3 保存并应用

修改后点击右下角 **Apply & Restart**，Docker 会自动重启。

> **注意**：分配内存不要超过 Mac 总内存的 70%，否则系统会频繁 swap 导致卡顿。

---

## 4. 验证

### 4.1 基础验证

```bash
# 查看 Docker 版本
docker --version
# 期望输出: Docker version 28.x.x, build xxx

# 查看 Docker Compose 版本
docker compose version
# 期望输出: Docker Compose version v2.x.x

# 查看系统信息
docker info
# 应显示 Server 状态为 running
```

### 4.2 运行测试容器

```bash
# 运行一个 Hello World 容器
docker run --rm hello-world
```

成功输出末尾应包含：
```
Hello from Docker!
This message shows that your installation appears to be working correctly.
```

### 4.3 运行交互式容器测试

```bash
# 运行一个 Ubuntu 容器，进去看一下
docker run -it --rm ubuntu:22.04 bash

# 容器内执行
root@xxx:/# cat /etc/os-release
# 应显示 Ubuntu 22.04

# 退出容器
root@xxx:/# exit
```

---

## 5. 常用命令速查

### 容器管理

```bash
# 运行容器（后台）
docker run -d --name mycontainer nginx

# 查看运行中的容器
docker ps

# 查看所有容器（含已停止）
docker ps -a

# 停止容器
docker stop mycontainer

# 启动已停止的容器
docker start mycontainer

# 删除容器
docker rm mycontainer

# 强制删除运行中的容器
docker rm -f mycontainer
```

### 镜像管理

```bash
# 拉取镜像
docker pull nginx:latest

# 查看本地镜像
docker images

# 删除镜像
docker rmi nginx:latest

# 清理未使用的镜像（释放空间）
docker image prune -a
```

### 数据与日志

```bash
# 查看容器日志
docker logs mycontainer

# 实时跟踪日志
docker logs -f mycontainer

# 进入运行中的容器
docker exec -it mycontainer bash

# 复制文件到容器
docker cp localfile.txt mycontainer:/path/

# 从容器复制文件到本地
docker cp mycontainer:/path/file.txt ./
```

### Docker Compose（多容器编排）

```bash
# 启动定义在 docker-compose.yml 中的服务
docker compose up -d

# 停止并删除
docker compose down

# 查看日志
docker compose logs -f

# 重建镜像（代码变更后）
docker compose up -d --build
```

---

## 6. 与已有服务的配合

### 6.1 Docker 与 Homebrew MySQL 共存

你的 Mac 上已经通过 Homebrew 安装了 MySQL，两者可以共存：

| 场景 | 使用 |
|------|------|
| 日常开发、数据持久化 | **Homebrew MySQL**（已在运行） |
| 临时测试、隔离环境、多版本 | **Docker MySQL** |

**Docker 运行临时 MySQL（不影响 Homebrew 版本）：**

```bash
# 运行一个独立的 MySQL 8 容器
docker run -d \
  --name mysql-test \
  -e MYSQL_ROOT_PASSWORD=123456 \
  -p 3307:3306 \
  mysql:8.0

# 连接（注意端口是 3307，避免与 Homebrew MySQL 的 3306 冲突）
mysql -h 127.0.0.1 -P 3307 -u root -p
```

### 6.2 运行 StarRocks（Docker 方案）

见 `skills/install-starrocks.md`，核心命令：

```bash
docker run -d \
  --name starrocks \
  --privileged \
  -p 9030:9030 \
  -p 8030:8030 \
  -v ~/starrocks-data:/data/deploy/starrocks \
  starrocks/allin1-ubuntu:3.3-latest
```

---

## 7. 故障排查

### Q1: Docker Desktop 启动后一直显示 "Docker Engine starting..."

**可能原因：**
- 上一次异常退出，锁文件残留
- 内存分配过高，系统资源不足

**解决：**
```bash
# 1. 完全退出 Docker Desktop（菜单栏鲸鱼图标 → Quit）
# 2. 清理锁文件
rm -rf ~/Library/Containers/com.docker.docker/Data/tasks

# 3. 重启 Docker Desktop
```

### Q2: 拉取镜像时报 `no matching manifest for linux/arm64`

**原因：** 镜像没有提供 arm64 版本。

**解决：** 开启 Rosetta 后，强制拉取 x86 版本：
```bash
docker pull --platform linux/amd64 <image-name>
```

### Q3: 容器内访问宿主机服务（如 Homebrew MySQL）

容器内不能用 `localhost` 访问宿主机，用以下地址：

```bash
# macOS Docker Desktop 专用域名
host.docker.internal

# 示例：容器内连接 Homebrew MySQL
mysql -h host.docker.internal -P 3306 -u root -p
```

### Q4: 磁盘空间不足

```bash
# 查看 Docker 占用空间
docker system df

# 清理所有未使用的数据（容器、镜像、卷、网络）
docker system prune -a --volumes

# ⚠️ 注意：这会删除所有未在运行的容器和未标记的镜像
```

### Q5: Docker Desktop 完全卸载

```bash
# 1. 退出 Docker Desktop
# 2. 删除应用程序
rm -rf /Applications/Docker.app

# 3. 清理配置和数据
rm -rf ~/Library/Containers/com.docker.docker
rm -rf ~/.docker
rm -rf ~/Library/Group\ Containers/group.com.docker
```

---

## 8. 配置文件路径速查

| 文件/目录 | 路径 |
|-----------|------|
| Docker Desktop App | `/Applications/Docker.app` |
| 用户配置 | `~/.docker/` |
| 容器数据 | `~/Library/Containers/com.docker.docker/` |
| Docker Compose 文件 | 项目目录下的 `docker-compose.yml` |
| Daemon 配置 | `~/.docker/daemon.json` |

---

## 参考

- [Docker Desktop for Mac 官方文档](https://docs.docker.com/desktop/setup/install/mac-install/)
- [Docker Hub](https://hub.docker.com/)
- [Docker Compose 文档](https://docs.docker.com/compose/)
