---
title: SQL 完整常用命令 Cheatsheet
date: 2026-04-11 02:16:58
tags: ['实施','数据库']
---

# SQL 完整常用命令 Cheatsheet

**目录导航：**

1. [数据库操作](#数据库操作)
2. [数据表操作](#数据表操作)
3. [数据操作（CRUD）](#数据操作crud)
4. [条件与排序](#条件与排序)
5. [聚合函数](#聚合函数)
6. [多表连接（JOIN）](#多表连接join)
7. [子查询](#子查询)
8. [索引与约束](#索引与约束)
9. [高级查询](#高级查询)
10. [事务与锁](#事务与锁)
11. [其他常用命令](#其他常用命令)

---

## 数据库操作

<details> <summary>展开/收起</summary>

```sql
-- 查看所有数据库
SHOW DATABASES;
-- 创建数据库
CREATE DATABASE db_name;

-- 删除数据库
DROP DATABASE db_name;

-- 使用数据库
USE db_name;

-- 查看当前数据库
SELECT DATABASE();
```



## 数据表操作

<details> <summary>展开/收起</summary>

```
-- 查看所有表
SHOW TABLES;

-- 创建表
CREATE TABLE table_name (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(50) NOT NULL,
    age INT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 删除表
DROP TABLE table_name;

-- 查看表结构
DESCRIBE table_name;

-- 修改表结构
ALTER TABLE table_name ADD COLUMN email VARCHAR(100);
ALTER TABLE table_name MODIFY COLUMN age SMALLINT;
ALTER TABLE table_name DROP COLUMN email;
```

</details>

------

## 数据操作（CRUD）

<details> <summary>展开/收起</summary>

```
-- 插入数据
INSERT INTO table_name (name, age) VALUES ('Alice', 25);

-- 查询数据
SELECT * FROM table_name;
SELECT name, age FROM table_name WHERE age > 20;
SELECT DISTINCT age FROM table_name;

-- 更新数据
UPDATE table_name SET age = 26 WHERE name = 'Alice';

-- 删除数据
DELETE FROM table_name WHERE age < 20;

-- 批量插入
INSERT INTO table_name (name, age) VALUES 
('Bob', 30),
('Charlie', 22);
```

</details>

------

## 条件与排序

<details> <summary>展开/收起</summary>

```
-- 条件查询
SELECT * FROM table_name WHERE age BETWEEN 20 AND 30;
SELECT * FROM table_name WHERE name LIKE 'A%';
SELECT * FROM table_name WHERE age IN (20, 25, 30);
SELECT * FROM table_name WHERE age IS NOT NULL;

-- 排序
SELECT * FROM table_name ORDER BY age ASC;
SELECT * FROM table_name ORDER BY age DESC, name ASC;
```

</details>

------

## 聚合函数

<details> <summary>展开/收起</summary>

```
-- 统计数量
SELECT COUNT(*) FROM table_name;

-- 平均值、最大值、最小值、总和
SELECT AVG(age) FROM table_name;
SELECT MAX(age) FROM table_name;
SELECT MIN(age) FROM table_name;
SELECT SUM(age) FROM table_name;

-- 分组统计
SELECT age, COUNT(*) AS count FROM table_name GROUP BY age;
SELECT age, AVG(salary) FROM employees GROUP BY age HAVING AVG(salary) > 5000;
```

</details>

------

## 多表连接（JOIN）

<details> <summary>展开/收起</summary>

```
-- 内连接
SELECT a.name, b.salary
FROM employees a
INNER JOIN salaries b ON a.id = b.emp_id;

-- 左连接
SELECT a.name, b.salary
FROM employees a
LEFT JOIN salaries b ON a.id = b.emp_id;

-- 右连接
SELECT a.name, b.salary
FROM employees a
RIGHT JOIN salaries b ON a.id = b.emp_id;

-- 自连接
SELECT a.name AS employee, b.name AS manager
FROM employees a
LEFT JOIN employees b ON a.manager_id = b.id;
```

</details>

------

## 子查询

<details> <summary>展开/收起</summary>

```
-- 单行子查询
SELECT name, age FROM employees WHERE salary = (SELECT MAX(salary) FROM employees);

-- 多行子查询
SELECT name FROM employees WHERE age IN (SELECT age FROM employees WHERE salary > 5000);

-- EXISTS 子查询
SELECT name FROM employees e WHERE EXISTS (SELECT 1 FROM salaries s WHERE s.emp_id = e.id AND s.salary > 5000);
```

</details>

------

## 索引与约束

<details> <summary>展开/收起</summary>

```
-- 创建索引
CREATE INDEX idx_name ON table_name(name);

-- 删除索引
DROP INDEX idx_name ON table_name;

-- 主键约束
ALTER TABLE table_name ADD PRIMARY KEY (id);

-- 外键约束
ALTER TABLE orders
ADD CONSTRAINT fk_user
FOREIGN KEY (user_id) REFERENCES users(id);

-- 唯一约束
ALTER TABLE table_name ADD CONSTRAINT unique_name UNIQUE(name);

-- 默认值
ALTER TABLE table_name MODIFY COLUMN status VARCHAR(20) DEFAULT 'active';
```

</details>

------

## 高级查询

<details> <summary>展开/收起</summary>

```
-- CASE 条件判断
SELECT name, 
       CASE 
           WHEN age < 18 THEN '未成年'
           WHEN age >= 18 AND age < 60 THEN '成年人'
           ELSE '老年'
       END AS age_group
FROM employees;

-- 窗口函数
SELECT name, salary, 
       RANK() OVER (ORDER BY salary DESC) AS rank_salary
FROM employees;

-- 合并查询
SELECT name, age FROM employees
UNION
SELECT name, age FROM contractors;

-- 分页查询（MySQL）
SELECT * FROM employees ORDER BY id LIMIT 10 OFFSET 20;
```

</details>

------

## 事务与锁

<details> <summary>展开/收起</summary>

```
-- 开始事务
START TRANSACTION;

-- 提交事务
COMMIT;

-- 回滚事务
ROLLBACK;

-- 锁表（MySQL）
LOCK TABLES table_name WRITE;
UNLOCK TABLES;
```

</details>

------

## 其他常用命令

<details> <summary>展开/收起</summary>

```
-- 清空表数据
TRUNCATE TABLE table_name;

-- 查看当前用户
SELECT USER();

-- 查看当前数据库
SELECT DATABASE();

-- 查看表记录数
SELECT COUNT(*) FROM table_name;

-- 日期函数示例
SELECT NOW();          -- 当前时间
SELECT CURDATE();      -- 当前日期
SELECT DATE_ADD(NOW(), INTERVAL 7 DAY); -- 日期加 7 天
SELECT DATE_FORMAT(NOW(), '%Y-%m-%d %H:%i:%s'); -- 自定义格式
```

</details> ```

```

```

```

```