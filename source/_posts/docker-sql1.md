---
title: Docker 创建与恢复 MySQL 数据库
date: 2026-04-21 02:36:47
tags: ['实施','数据库']
---

# Docker 创建与恢复 MySQL 数据库

本文介绍如何在 Docker 中创建 MySQL 容器、初始化数据库，以及从备份文件恢复数据库。  

---

## 目录

1. [创建 MySQL 容器](#创建-mysql-容器)
2. [初始化数据库](#初始化数据库)
3. [备份数据库](#备份数据库)
4. [恢复数据库](#恢复数据库)
5. [常用注意事项](#常用注意事项)

---

## 创建 MySQL 容器

<details>
<summary>展开/收起</summary>

### 拉取 MySQL 镜像
```bash
docker pull mysql:8.0
```
### 创建并运行容器
```
docker run -d \
--name my-mysql \
-p 3306:3306 \
-e MYSQL_ROOT_PASSWORD=yourpassword \
-e MYSQL_DATABASE=testdb \
-e MYSQL_USER=testuser \
-e MYSQL_PASSWORD=testpass \
-v /my/local/datadir:/var/lib/mysql \
mysql:8.0
```
**说明：**
- `--name my-mysql`：容器名称。
- `-p 3306:3306`：将容器 MySQL 端口映射到主机 3306。
- `MYSQL_ROOT_PASSWORD`：root 用户密码。
- `MYSQL_DATABASE`：容器启动时初始化创建的数据库。
- `MYSQL_USER` 和 `MYSQL_PASSWORD`：新建普通用户及密码。
- `-v /my/local/datadir:/var/lib/mysql`：数据卷挂载，保证数据库持久化。
### 查看容器状态
```
docker ps
```
</details>

------

## 初始化数据库

<details> <summary>展开/收起</summary>

### 进入 MySQL 容器

```
docker exec -it my-mysql bash
```

### 登录 MySQL

```
mysql -u root -p
```

输入 `MYSQL_ROOT_PASSWORD` 设置的密码，即可登录。

### 创建数据库或用户（可选）

```
CREATE DATABASE example_db;
CREATE USER 'example_user'@'%' IDENTIFIED BY 'example_pass';
GRANT ALL PRIVILEGES ON example_db.* TO 'example_user'@'%';
FLUSH PRIVILEGES;
```

</details>

------

## 备份数据库

<details> <summary>展开/收起</summary>
### 使用 `docker exec` 备份

```
docker exec my-mysql /usr/bin/mysqldump -u root -p testdb > /my/local/backup/testdb.sql
```

- `testdb`：要备份的数据库名。
- `/my/local/backup/testdb.sql`：备份文件路径（主机路径）。

### 使用 `docker exec` 结合容器内路径备份

```
docker exec my-mysql sh -c 'exec mysqldump -u root -p"$MYSQL_ROOT_PASSWORD" testdb' > testdb.sql
```
</details>

------

## 恢复数据库

<details> <summary>展开/收起</summary>

### 使用 `docker exec` 恢复

```
docker exec -i my-mysql mysql -u root -p testdb < /my/local/backup/testdb.sql
```

### 示例说明

- `-i`：保持标准输入打开，将 SQL 文件传入容器。
- `testdb`：目标数据库名，恢复到这个数据库。
- `/my/local/backup/testdb.sql`：SQL 文件路径。

### 进入容器恢复（可选）

```
docker exec -it my-mysql bash
mysql -u root -p testdb < /var/lib/mysql/testdb.sql
```

</details>

------

## 常用注意事项

<details> <summary>展开/收起</summary>

1. **持久化数据**：一定要使用 `-v /host/path:/var/lib/mysql`，否则容器删除后数据也会丢失。
2. **端口冲突**：宿主机如果已有 MySQL 监听 3306，映射端口需要修改。
3. **权限问题**：备份恢复时确保文件权限允许 Docker 读取。
4. **SQL 文件编码**：如果包含中文，确保 SQL 文件是 UTF-8 编码，否则可能出现乱码。
5. **容器管理**：常用命令：

```
docker stop my-mysql       # 停止容器
docker start my-mysql      # 启动容器
docker restart my-mysql    # 重启容器
docker logs -f my-mysql    # 查看日志
```

</details> 