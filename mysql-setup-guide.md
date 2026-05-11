# MySQL Server 安装与初始化指南（macOS + Homebrew）

> 适用场景：本地开发环境快速搭建 MySQL 8.0+
> 预计时间：5-10 分钟
> 更新时间：2026-05-11

---

## 1. 安装 MySQL

```bash
# 通过 Homebrew 安装
brew install mysql

# 验证安装
mysql --version
```

> **常见问题**：若 Homebrew 目录权限不足（`Operation not permitted`），执行：
> ```bash
> sudo chown -R $(whoami) /opt/homebrew
> ```

---

## 2. 启动 MySQL 服务

### 推荐方式：brew services（可靠、自动守护）

```bash
# 启动并设为开机自启
brew services start mysql

# 停止
brew services stop mysql

# 重启
brew services restart mysql

# 查看状态
brew services list | grep mysql
```

**优点：**
- 使用 macOS `launchd` 守护进程管理，崩溃自动重启
- PID 文件处理可靠，不会残留
- 开机自动启动，无需手动干预

### 备用方式：mysql.server（不推荐日常使用）

```bash
# 启动
mysql.server start

# 停止（⚠️ 已知问题：可能报 PID 文件错误）
mysql.server stop
```

**已知问题：** `mysql.server stop` 在 macOS 上经常出现以下错误：

```
ERROR! The server quit without updating PID file
(/opt/homebrew/var/mysql/xxx.local.pid)
```

**原因：**
- MySQL 进程实际已停止，但 PID 文件未删除
- 或之前异常退出导致 PID 文件残留
- 与 macOS 文件权限/ sandbox 机制冲突

**解决方法：**

```bash
# 方法 1：确认进程是否还在
lsof -i :3306

# 方法 2：如果进程已停，手动删除残留 PID 文件
sudo rm -f /opt/homebrew/var/mysql/*.pid

# 方法 3：直接用 brew services 重启（推荐）
brew services restart mysql
```

> **建议**：日常开发只用 `brew services`，不要用 `mysql.server`。

---

## 3. 初始化安全配置（推荐）

### 3.1 设置 root 密码

```bash
mysql_secure_installation
```

按提示设置 root 密码，并选择：
- Remove anonymous users? → **Y**
- Disallow root login remotely? → **Y**
- Remove test database? → **Y**
- Reload privilege tables? → **Y**

### 3.2 配置免密登录（本地开发便利）

使用 `mysql_config_editor` 加密存储凭证，避免明文密码：

```bash
mysql_config_editor set \
  --login-path=local \
  --host=localhost \
  --user=root \
  --password
```

输入 root 密码后，即可免密连接：

```bash
mysql --login-path=local
```

---

## 4. 创建数据库

```bash
mysql --login-path=local -e "
CREATE DATABASE IF NOT EXISTS mamba
  DEFAULT CHARACTER SET utf8mb4
  COLLATE utf8mb4_unicode_ci;
"

# 验证
mysql --login-path=local -e "SHOW DATABASES LIKE 'mamba';"
```

---

## 5. 创建专用用户

### 5.1 调整密码策略（开发环境）

MySQL 8 默认密码策略较严格，开发环境可放宽：

```bash
# 临时调整（重启后失效）
mysql --login-path=local -e "
SET GLOBAL validate_password.policy = LOW;
SET GLOBAL validate_password.length = 4;
"
```

**永久生效（推荐）：**

```bash
sudo mkdir -p /opt/homebrew/etc
sudo tee /opt/homebrew/etc/my.cnf << 'EOF'
[mysqld]
validate_password.policy=LOW
validate_password.length=4
EOF

brew services restart mysql
```

### 5.2 创建用户并授权

```bash
mysql --login-path=local -e "
CREATE USER IF NOT EXISTS 'mamba_user'@'localhost'
  IDENTIFIED BY '你的密码';

GRANT ALL PRIVILEGES ON mamba.*
  TO 'mamba_user'@'localhost';

FLUSH PRIVILEGES;
"
```

### 5.3 验证

```bash
# 查看用户
mysql --login-path=local -e "
SELECT user, host FROM mysql.user WHERE user='mamba_user';
"

# 测试新用户连接
mysql -u mamba_user -p -e "SHOW DATABASES;"
```

---

## 6. 连接信息速查

| 项 | 值 |
|---|---|
| host | `localhost` |
| port | `3306` |
| user | `mamba_user` |
| password | 你设置的密码 |
| database | `mamba` |
| charset | `utf8mb4` |

**Python SQLAlchemy:**
```python
DATABASE_URL = "mysql+pymysql://mamba_user:密码@localhost:3306/mamba?charset=utf8mb4"
```

**Node.js mysql2:**
```javascript
const connection = mysql.createConnection({
  host: 'localhost',
  user: 'mamba_user',
  password: '密码',
  database: 'mamba',
  charset: 'utf8mb4_unicode_ci'
});
```

---

## 7. 权限说明

| 数据库 | 用途 | 是否需授权 |
|---|---|---|
| `mamba` | 业务数据库 | ✅ 已授权给 `mamba_user` |
| `information_schema` | 元数据视图 | ❌ 自动可读 |
| `performance_schema` | 性能监控 | ❌ 不需要 |
| `mysql` | 用户/权限系统 | ❌ **绝对不给** |

---

## 8. 常用命令

```bash
# 连接
mysql --login-path=local

# 查看所有数据库
mysql --login-path=local -e "SHOW DATABASES;"

# 查看所有用户
mysql --login-path=local -e "SELECT user, host FROM mysql.user;"

# 导出数据库
mysqldump -u mamba_user -p mamba > mamba_backup.sql

# 导入数据库
mysql -u mamba_user -p mamba < mamba_backup.sql

# 查看运行状态
mysql --login-path=local -e "SHOW STATUS LIKE 'Threads_connected';"
```

---

## 9. 故障排查

### Q1: `mysql.server stop` 报 PID 文件错误

```bash
# 确认 MySQL 是否还在运行
lsof -i :3306
mysql --login-path=local -e "SELECT 1;"

# 如果已停止，删除残留 PID 文件
sudo rm -f /opt/homebrew/var/mysql/*.pid

# 改用 brew services 管理（推荐长期方案）
brew services restart mysql
```

### Q2: 连接时报 `Can't connect through socket`

```bash
# 确认服务状态
brew services list | grep mysql

# 如果状态是 error，重启
brew services restart mysql
```

### Q3: 忘记 root 密码

```bash
# 1. 停止服务
brew services stop mysql

# 2. 跳过权限启动
mysqld_safe --skip-grant-tables &

# 3. 无密码登录，重置密码
mysql -u root
FLUSH PRIVILEGES;
ALTER USER 'root'@'localhost' IDENTIFIED BY '新密码';

# 4. 正常重启
brew services restart mysql
```

---

## 参考

- [MySQL 8.0 官方文档](https://dev.mysql.com/doc/refman/8.0/en/)
- [Homebrew MySQL Formula](https://formulae.brew.sh/formula/mysql)
