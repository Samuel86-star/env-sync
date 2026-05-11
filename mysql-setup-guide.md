# MySQL Server 安装与初始化指南（macOS + Homebrew）

> 适用场景：本地开发环境快速搭建 MySQL 8.0+
> 预计时间：5-10 分钟

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

```bash
# 启动并设为开机自启
brew services start mysql

# 验证状态
brew services list | grep mysql

# 手动启停（可选）
mysql.server start
mysql.server stop
```

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

## 参考

- [MySQL 8.0 官方文档](https://dev.mysql.com/doc/refman/8.0/en/)
- [Homebrew MySQL Formula](https://formulae.brew.sh/formula/mysql)
